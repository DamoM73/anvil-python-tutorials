# Get Chart Function

```{topic} In this tutorial you will:
- Understand how to cache data for efficient retrieval.
- Create a private variable to store the chart.
- Implement a method to check if the chart is cached and retrieve it if available, or regenerate it if not.
- Modify the **CalendarComponent** to utilize the cached chart.
- Test the implementation to ensure efficiency.
```

The final frontend component that retrieves data from the backend is the **CalendarComponent**. In particular it retrieves the Gantt chart using the **get_chart** method.

## Planning

Just like the previous data, we need to cache the diagram so that it only gets retrieved:

- the first time **CalendarComponent** is called.
- when the data in the Assessments table is updated.

This should be very familiar by now, so let's get to the coding

## Coding

First we need to create a variable in the **data_access** module to store the diagram.

1. Open the **data_access** module
2. Add the highlighted line to the bottom of the **cached values** section.

```{code-block} python
:linenos:
:lineno-start: 7
:emphasize-lines: 4

# cached values
__user = None
__assessments = None
__chart = None
```

```{admonition} Code explaination
:class: notice
- **line 9** &rarr; creates the private variable `__chart` to store the diagram
```

Then we need to create a method that will cache and return the chart.

3. At the bottom of the **data_access** module add the highlighted code

```{code-block} python
:linenos:
:lineno-start: 70
:emphasize-lines: 1 - 10
def get_chart():
  global __chart

  if __chart:
    print("Using cached chart")
    return __chart

  print("Building new chart from database")
  __chart = anvil.server.call('get_chart', user, assessments)
  return __chart
```

```{admonition} Code explaination
:class: notice
- **line 70** &rarr; creates the **get_chart** method
- **line 71** &rarr; allows the method to edit the value of `__chart`
- **line 73** &rarr; checks if the chart is already cached
- **line 74** &rarr; informs the developer that the cached chart is being used
- **line 75** &rarr; returns the cached chart and ends the method
- **line 77** &rarr; informs the developed that a new chart is being built
- **line 78** &rarr; builds a new chart and stores it in the `__chart` variable
- **line 79** &rarr; returned the cached chart
```

We also need to re-cache the chart when the assessments data is updated.

4. Go to the **my_assessment** function and add the highlighted line of code

```{code-block} python
:linenos:
:lineno-start: 55
:emphasize-lines: 14
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
```

```{admonition} Code explaination
:class: notice
- **line 68** &rarr; reset `__chart` to `None` meaning the next time **get_chart** is called, a new chart will be built and cached.
```

Finally we need to replace the call to backend in **CalendarComponent**

5. Open the **CalendarComponent** in **Code** mode
6. Change the highlighted code in the **load_chart** method

```{code-block} python
:linenos:
:lineno-start: 26
:emphasize-lines: 2
  def load_chart(self):
    fig = data_access.get_chart()
        
    # Assign the Plotly figure to the Anvil Plot component
    self.plot_timeline.figure = fig
```

```{admonition} Code explaination
:class: notice
**line 27** &rarr; retrieves the cached chart
```

## Testing

Launch your web site for testing.

1. Navigate to the **Calendar** page (this should still load slowly)
2. Navigate back to the **Home** page (this should be quick)
3. Navigate back to the **Calendar** page (this should be quick now)

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
  
  if __assessments:
    print("Using cached assessments")
    return __assessments

  print("Accessing assessments from database")
  __assessments = anvil.server.call('get_assessment')
  return __assessments

def add_assessment(subject, details, start_date, due_date):
  global __assessments
  
  print("Writing assessment details to the database")
  anvil.server.call('add_assessment', subject, details, start_date, due_date)
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
  __chart = anvil.server.call('get_chart')
  return __chart
  ```

### Final CalendarComponent

```{code-block} python
:linenos:
from ._anvil_designer import CalendarComponentTemplate
from anvil import *
import plotly.graph_objects as go
import anvil.server
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.users
from .. import data_access


class CalendarComponent(CalendarComponentTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)

    # Any code you write here will run before the form opens.
    if data_access.the_user():
      self.card_details.visible = True
      self.card_error.visible = False
      self.load_chart()
    else:
      self.card_details.visible = False
      self.card_error.visible = True

  def load_chart(self):
    fig = data_access.get_chart()
        
    # Assign the Plotly figure to the Anvil Plot component
    self.plot_timeline.figure = fig
```
