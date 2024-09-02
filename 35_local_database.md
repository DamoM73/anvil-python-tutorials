# Reduce Local Database Access

```{topic} In this tutorial you will:
-
```

Remember when we discussed optimisation, we focused on preventing the frontend from unnecessarily retrieving data from the backend. This was a low-hanging fruit in improving our website speed, since the communication between the frontend and the backend is the slowest communication that occurs in our web app. 

## Planning

Now that we have addressed this, we can look at other slow routes of data communication. To do this we need to understand the different speed of data retrieval:

1. Fastest - retrieve from RAM (memory)
2. Medium - retrieve from storage (HDD / SSD / Servers)
3. Slowest - retrieve over internet

![frontend backend](./assets/img/02/frontend_backend.png)

Looking at the diagram of our web app we can see that there are two more places that communication occurs:

- On the frontend between the application and the device
- On the backend between the server and the database

The frontend communication occurs by retrieving data from the RAM, so we can't get a significant increase in speed here, but the backend communication involves server retrieval from storage, so we can speed that up.

In our current **assessment_service**, the database is unnecessarily calls cached data in:

- **add_assessment** - user
- **get_assessment** - user
- **get_chart** - user and chart

In all these cases, the cached data can be passed to the server when the **assessment_service** method is called.

## Code

### add_assessment function

Lets start with the add_assessment.

1. Open the **assessment_service** module and go to **add_assessment**
2. Delete the `anvil.users.get_user()` call in **line 11**
3. Add `user` as an argument to be passed - like the highlight below

```{code-block} python
:linenos:
:lineno-start: 9
:emphasize-lines: 2
@anvil.server.callable
def add_assessment(user, subject, details, start_date, due_date):
  app_tables.assessments.add_row(user= user,
                                 subject= subject,
                                 details=details,
                                 start_date=start_date,
                                 due_date=due_date,
                                 completed=False)
```

```{admonition} Code explaination
:class: notice
**line 10** &rarr; adds `user` as a value to be passed when the method is called
```

Now we have to change the call to **add_assessment** to include the `user`

4. Open the **data_access** module
5. Add **line 51** below and change **line 52**

```{code-block} python
:linenos:
:lineno-start: 47
:emphasize-lines: 5-6
def add_assessment(subject, details, start_date, due_date):
  global __assessments
  
  print("Writing assessment details to the database")
  user = the_user()
  anvil.server.call('add_assessment', user, subject, details, start_date, due_date)
  __assessments = None
  my_assessment()
```

```{admonition} Code explaination
:class: notice
- **line 51** &rarr; retrieves the cached user data
- **line 52** &rarr; adds the cached user data to the arguments passed to **add_assessment**
```

#### Testing add_assessment function

Launch your website and add a new assessment item.

Notice all the caching messages - think of the time you are saving.

## get_assessment function

New we will use caching data in the get_assessment function.

1. Open the **assessment_service** module and go to **get_assessment**
2. Delete the `anvil.users.get_user()` call in **line 20**
3. Add `user` as an argument to be passed - like the highlight below

```{code-block} python
:linenos:
:lineno-start: 18
:emphasize-lines: 2
@anvil.server.callable
def get_assessment(user):
  return app_tables.assessments.search(tables.order_by('due_date'),
                                      user=user,
                                      completed=False)
```

```{admonition} Code explaination
:class: notice
**line 19** &rarr; adds `user` as a value to be passed when the method is called
```

Now we have to change the call to **my_assessment** to include the `user`

4. Open the **data_access** module
5. Add **line 39** below and change **line 46**

```{code-block} python
:linenos:
:lineno-start: 36
:emphasize-lines: 4, 11
def my_assessment():
  global __assessments

  user = the_user()
  
  if __assessments:
    print("Using cached assessments")
    return __assessments

  print("Accessing assessments from database")
  __assessments = anvil.server.call('get_assessment', user)
  return __assessments
```

```{admonition} Code explaination
:class: notice
- **line 39** &rarr; retrieves the cached user data
- **line 46** &rarr; adds the cached user data to the arguments passed to **get_assessment**
```

### Testing get_assessment function

Launch your web app and check that pages that use assessment data:

- all the assessments load on the **Home** page
- the **Calendar** page

## get_chart function

Finally the get_chart function:

1. Open the **assessment_service** module and go to 
2. Delete the `anvil.users.get_user()` call in **line 43**
3. Delete the `app_tables.assessments.search` call in **lines 44 - 46**
4. Add `assessments` as arguments to be passed - like the highlight below

```{code-block} python
:linenos:
:lineno-start: 40
:emphasize-lines: 2
@anvil.server.callable
def get_chart(assessments):
    
    # Create a DataFrame from the assessments data
    data = []
    for assessment in assessments:
        # adjust for exams
        start_date = assessment['start_date']
        due_date = assessment['due_date']
                
        if start_date == due_date:
            due_date += pd.Timedelta(days=1)
      
        data.append({
            "Subject": assessment['subject'],
            "Details": assessment['details'],
            "Start": assessment['start_date'],
            "Due": due_date
        })
    
    df = pd.DataFrame(data)
```

```{admonition} Code explaination
:class: notice
**line 19** &rarr; adds `assessments` as a value to be passed when the method is called
```

Now we have to change the call to **my_assessment** to include the `user`

4. Open the **data_access** module
5. Add **line 82** below and change **line 83**

```{code-block} python
:linenos:
:lineno-start: 74
:emphasize-lines: 9, 10
def get_chart():
  global __chart

  if __chart:
    print("Using cached chart")
    return __chart

  print("Building new chart from database")
  assessments = my_assessment()
  __chart = anvil.server.call('get_chart', assessments)
  return __chart
```

```{admonition} Code explaination
:class: notice
- **line 82** &rarr; retrieves the cached assessments data
- **line 83** &rarr; adds the cached user data to the arguments passed to **get_chart**
```

## Testing get_chart function

Launch your website, and navigate to the **Calendar** page.

## Final code state

By the end of this tutorial your code should be the same as below:

### Final data_access

```{code-block} python
:linenos:
import anvil.server
import anvil.users
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables

# cached values
__user = None
__assessments = None
__chart = None

def the_user():
  global __user

  if __user:
    print("Using cached user")
    return __user

  print("Accessing user from database")
  __user = anvil.users.get_user()
  return __user

def logout():
  global __user
  __user = None
  anvil.users.logout()

def update_user(first_name, last_name):
  global __user
  
  print("Writing user details to database")
  anvil.server.call('update_user', first_name, last_name)
  __user = None
  __user = the_user()

def my_assessment():
  global __assessments

  user = the_user()
  
  if __assessments:
    print("Using cached assessments")
    return __assessments

  print("Accessing assessments from database")
  __assessments = anvil.server.call('get_assessment', user)
  return __assessments

def add_assessment(subject, details, start_date, due_date):
  global __assessments
  
  print("Writing assessment details to the database")
  user = the_user()
  anvil.server.call('add_assessment', user, subject, details, start_date, due_date)
  __assessments = None
  my_assessment()

def update_assessment(assessment_id, subject, details, start_date, due_date, completed):
  global __assessments

  print("Updating assessment details on the database")
  anvil.server.call('update_assessment',
                    assessment_id,
                    subject,
                    details,
                    start_date,
                    due_date,
                    completed
                   )
  __assessments = None
  __chart = None
  my_assessment()

def get_chart():
  global __chart

  if __chart:
    print("Using cached chart")
    return __chart

  print("Building new chart from database")
  assessments = my_assessment()
  __chart = anvil.server.call('get_chart', assessments)
  return __chart
  ```

  ### Final assessment_service

```{code-block} python
:linenos:
import anvil.users
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.server
import plotly.express as px
import pandas as pd

@anvil.server.callable
def add_assessment(user, subject, details, start_date, due_date): 
  app_tables.assessments.add_row(user= user,
                                 subject= subject,
                                 details=details,
                                 start_date=start_date,
                                 due_date=due_date,
                                 completed=False)

@anvil.server.callable
def get_assessment(user):
  return app_tables.assessments.search(tables.order_by('due_date'),
                                      user=user,
                                      completed=False)

@anvil.server.callable
def update_assessment_completed(assessment_id, completed):
  assessment = app_tables.assessments.get_by_id(assessment_id)
  if assessment:
    assessment["completed"] = completed

@anvil.server.callable
def update_assessment(assessment_id, subject, details, start_date, due_date, completed):
    assessment = app_tables.assessments.get_by_id(assessment_id)
    if assessment:
        assessment['subject'] = subject
        assessment['details'] = details
        assessment['start_date'] = start_date
        assessment['due_date'] = due_date
        assessment['completed'] = completed

@anvil.server.callable
def get_chart(assessments):
    
    # Create a DataFrame from the assessments data
    data = []
    for assessment in assessments:
        # adjust for exams
        start_date = assessment['start_date']
        due_date = assessment['due_date']
                
        if start_date == due_date:
            due_date += pd.Timedelta(days=1)
      
        data.append({
            "Subject": assessment['subject'],
            "Details": assessment['details'],
            "Start": assessment['start_date'],
            "Due": due_date
        })
    
    df = pd.DataFrame(data)
    
    # Create the Gantt chart using Plotly
    fig = px.timeline(df, 
                      x_start="Start", 
                      x_end="Due", 
                      y="Subject", 
                      text="Details", 
                      title="Assessment Schedule"
                     )

    fig.update_yaxes(title_text="") 
    
    return fig
```