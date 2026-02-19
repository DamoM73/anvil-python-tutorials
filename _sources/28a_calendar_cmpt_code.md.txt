# CalendarComponent Code

```{topic} In this tutorial you will:
- Retrieving and processing assessment data using Pandas.
- Creating a timeline chart with Plotly.
- Integrating the chart into an Anvil app.
- Handling special cases, such as exams with identical start and due dates.-
- Refining chart display by adjusting axes and labels.
```

A note before we start. In this tutorial we will be using features of two libraries beyond the scope of course.

- **Pandas** - a fast and flexible Python library for data manipulation and analysis
- **Plotly** - a versatile Python library for creating interactive, web-based data visualizations

Plotly is the library that supports Anvil's plots, while Pandas provides the computing backbone for Plotly. We will only be scraping the surface of both of these libraries, so their functions will not be explained in detail.

If you wish to know more about these libraries, then you can do so through the following tutorials:

- **<a href="https://www.freecodecamp.org/news/learn-pandas-for-data-science/" target="_blank">Learn Pandas & Python for Data Analysis</a>**
- **<a href="https://www.geeksforgeeks.org/python-plotly-tutorial/" target="_blank">Plotly tutorial</a>**

## Plan

The nature of Plotly means that we will be creating the chart on the backend, and then sending it to frontend to be displayed. Therefore the majority of our code will be written in the **assessment_service** module.

We will need to create a **get_chart** method that:

- retrieve details from the **assessments** table
- create a **dataframe** for the assessment details
- use Plotly to create a timeline chart from the dataframe
- send the chart back to the frontend

```{admonition} Dataframes
:class: note
DataFrames are digital tables where Pandas stores, organises, and works with data, just like you would with rows and columns in a spreadsheet.
```

Now we have worked out what we need to do, we can start coding.

## Coding

### get_chart

To code the **get_chart** method we need to:

1. Open the **assessment_service** server module
2. add the highlighted code to the import section

```{code-block} python
:linenos:
:lineno-start: 1
:emphasize-lines: 6-7
import anvil.users
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.server
import plotly.express as px
import pandas as pd
```

```{admonition} Code explaination
:class: notice
- **line 6** &rarr; imports the plotly.express library and assigns it the **px** alias
- **line 7** &rarr; imports the pandas library and assigns it the **pd** alias
```

```{admonition} Import aliases
:class: note
Import aliases in Python allow you to import a module or a function using a shorter or more convenient name, making your code easier to write and read. 
```

3. move to the bottom of the **assessments_service** 
4. the highlighted code to retrieve the necessary data

```{code-block} python
:linenos:
:lineno-start: 44
:emphasize-lines: 1-7
@anvil.server.callable
def get_chart():
    # Fetch assessments from the data table
    user = anvil.users.get_user()
    assessments = app_tables.assessments.search(tables.order_by('due_date'),
                                       user=user,
                                       completed=False)
```

```{admonition} Code explaination
:class: notice
- **line 44** &rarr; makes **get_chart** callable from the frontend
- **line 45** &rarr; creates the **get_chart** method
- **line 47** &rarr; gets the current user
- **line 48** &rarr; retrieves all the outstanding assessments and stores them in the **assessments** variable
```

5. to process the data, add the code highlighted below

```{code-block} python
:linenos:
:lineno-start: 44
:emphasize-lines: 9 - 19
@anvil.server.callable
def get_chart():
    # Fetch assessments from the data table
    user = anvil.users.get_user()
    assessments = app_tables.assessments.search(tables.order_by('due_date'),
                                       user=user,
                                       completed=False)
    
    # Create a DataFrame from the assessments data
    data = []
    for assessment in assessments:
        data.append({
            "Subject": assessment['subject'],
            "Details": assessment['details'],
            "Start": assessment['start_date'],
            "Due": assessment['due_date']
        })
    
    df = pd.DataFrame(data)
```

```{admonition} Code explaination
:class: notice
- **line 53** &rarr; creates the **data** list to be used for processing the data
- **line 54** &rarr; iterates over all the assessments stored in the **assessments** variable
- **line 55 - 60** &rarr; creates a dictionary of each assessment and then adds this to the **data** list
- **line 62** &rarr; converts all the assessment dictionaries in a dataframe
```

6. add the highlighted code to create the chart

```{code-block} python
:linenos:
:lineno-start: 44
:emphasize-lines: 21 - 30
@anvil.server.callable
def get_chart():
    # Fetch assessments from the data table
    user = anvil.users.get_user()
    assessments = app_tables.assessments.search(tables.order_by('due_date'),
                                       user=user,
                                       completed=False)
    
    # Create a DataFrame from the assessments data
    data = []
    for assessment in assessments:
        data.append({
            "Subject": assessment['subject'],
            "Details": assessment['details'],
            "Start": assessment['start_date'],
            "Due": assessment['due_date']
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
    
    return fig
```

```{admonition} Code explaination
:class: notice
- **lines 65 - 71** &rarr; creates the timeline chart and stores it in the variable **fig**
  - **df** &rarr; the dataframe for the chart
  - **x_start="Start"** &rarr; identifies the dataframe column which will be used at the starting point for each bar
  - **x_end="Due"** &rarr; identifies the dataframe column which will be used at the end point for each bar
  - **y="Subject"** &rarr; provide the column that will be the y axis
  - **text="Details"** &rarr; labels each bar with the details
- **line 73** &rarr; send the chart to the frontend
```

### CalendarComponent

Now that we have a backend method that provides the chart, we need to call that from the **CalendarComponent**:

1. Open **calendarComponent** in the **Code** mode.
2. Add the highlighted code to create the **load_chart** method

```{code-block} python
:linenos:
:lineno-start: 24
:emphasize-lines: 1 - 5
  def load_chart(self):
    fig = anvil.server.call('get_chart')
        
    # Assign the Plotly figure to the Anvil Plot component
    self.plot_timeline.figure = fig
```

```{admonition} Code explaination
:class: notice
- **line 24** &rarr; creates the **load_chart** method
- **line 25** &rarr; gets the chart by calling the **get_chart** backend function we just created
- **line 28** &rarr; displays the chart on the **self.plot_timeline**
```

3. Finally we need to call the **load_chart** method so it displays when the component is loaded
4. Go to the bottom of the `__init__` method and had the highlighted line

```{code-block} python
:linenos:
:lineno-start: 12
:emphasize-lines: 9
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)

    # Any code you write here will run before the form opens.
    if anvil.users.get_user():
      self.card_details.visible = True
      self.card_error.visible = False
      self.load_chart()
    else:
      self.card_details.visible = False
      self.card_error.visible = True
```

```{admonition} Code explaination
:class: notice
- **line 20** &rarr; runs the **load_chart** method when the **Calendar Component** is loaded.
```

### Testing

Time to test all that code. Launch your web app and navigate to the Calendar page. You should see something similar to below.

![test](./assets/img/28a/test_1.png)

If you look closely, there are a couple of problems with our chart.

1. There is no bar for the English exam
2. The y-axis title (subject) is unnecessary

![test](./assets/img/28a/test_problems.png)

So lets fix those two.

### Exams not appearing

The reason that exams do not appear on the chart, is that the start date and the due date are the same. The timeline chart plots the time between the start and finish, and in the case of exams, this is nothing.

The easiest way to solve this problem is to move the due date back one day. But be careful, we only want to do this for exams, ie. where the due date is the same as the start date.

Lets implement this:

1. Open the **assessment_service** server module
2. Add the highlighted code inside the dataframe creation code

```{code-block} python
:linenos:
:lineno-start: 52
:emphasize-lines: 4-9
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
            "Due": assessment['due_date']
        })
    
    df = pd.DataFrame(data)
```

```{admonition} Code explaination
:class: notice
- **line 56** &rarr; stores the start date in the **start_date** variable
- **line 57** &rarr; stores the due date in the **due_date** variable
- **line 59** &rarr; checks to see if the start date and due date are the same
- **line 60** &rarr; uses Pandas' **Timedelta** method to add one day to the **due_date**
```

Now we have adjusted the start and due dates, we need to put these values into the diction before it is added to the data list.

3. Adjust the highlighted line of code

```{code-block} python
:linenos:
:lineno-start: 52
:emphasize-lines: 15
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
- **line 66** &rarr; add our adjusted **due_date** to the dataframe
```

#### Testing Exams

Launch your web app and navigate to the calendar page (note: make sure you have added an exam assessment). You should now see your exams.

![exam test](./assets/img/28a/test_exam.png)

### y-axis title

Plotly provides a wide range of formatting options. These options can be found in the **<a href="https://anvil.works/docs/ui/components/plots#configuring-plots" target="_blank">Anvil documentation</a>** or the **<a href="https://plot.ly/python/" target="_blank">Plotly documentation</a>**.

We will only be concerned with removing the y-axis title.

1. Open the **assessment_service**
2. At the bottom of the creating Gantt chart section add the highlighted code.

```{code-block} python
:linenos:
:lineno-start: 71
:emphasize-lines: 10
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

```{admonition} Code explaination
:class: notice
- **line 80** &rarr; makes the y-axis blank
```

#### Test the y-axis

Once again launch you web app and check that the y-axis title has gone.

![test y-axis](./assets/img/28a/testing_y.png)

## Final code state

By the end of this tutorial your code should be the same as below:

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


class CalendarComponent(CalendarComponentTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)

    # Any code you write here will run before the form opens.
    if anvil.users.get_user():
      self.card_details.visible = True
      self.card_error.visible = False
      self.load_chart()
    else:
      self.card_details.visible = False
      self.card_error.visible = True

  def load_chart(self):
    fig = anvil.server.call('get_chart')
        
    # Assign the Plotly figure to the Anvil Plot component
    self.plot_timeline.figure = fig
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
def add_assessment(subject, details, start_date, due_date):
  user = anvil.users.get_user()
  
  app_tables.assessments.add_row(user= user,
                                 subject= subject,
                                 details=details,
                                 start_date=start_date,
                                 due_date=due_date,
                                 completed=False)

@anvil.server.callable
def get_assessment():
  user = anvil.users.get_user()

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
def get_chart():
    # Fetch assessments from the data table
    user = anvil.users.get_user()
    assessments = app_tables.assessments.search(tables.order_by('due_date'),
                                       user=user,
                                       completed=False)
    
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