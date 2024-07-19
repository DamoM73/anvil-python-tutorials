# Add Component Code

```{topic} In this tutorial you will:
- 
```

Now that we have create the layout for the AddComponent, we need to write the code to run it. Eventually we want to save the details to a table, but we haven't made a table yet, so we will simply display the data as a save message

## Planning

There are two events that will trigger code:

- loading the component &rarr; initialise variable to store the user input
- clicking the **Add Assessment** button &rarr; validate the user's input, display save message and reset the form.

## Coding

Now we know what we are doing, open the **AddComponent** in Code mode.

### Initialising variable

We need to create variables to store all the user's input. This needs to occur in the `__init__` method.

```{code-block} python
:linenos:
:lineno-start: 10
:emphasize-lines: 5 - 8
class AddComponent(AddComponentTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)
    self.subject = ""
    self.details = ""
    self.start = None
    self.due = None
```

```{admonition} Code explaination
:class: notice
- **lines 14 & 15** &rarr; since **Subject** and **Details** store text, we create empty strings for their variables.
- **lines 16 & 15** &rarr; **Start** and **Due** are both dates, which are objects, so their empty variables are set using `None`
```

We also need to make sure the error message is invisible.

```{code-block} python
:linenos:
:lineno-start: 10
:emphasize-lines: 11
class AddComponent(AddComponentTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)
    self.subject = ""
    self.details = ""
    self.start = None
    self.due = None

    # Any code you write here will run before the form opens.
    self.label_error.visible = False
```

```{admonition} Code explaination
:class: notice
- **line 20** &rarr; sets the label error to not vissible
```

### Validation

Now we need to make the `button_add_click` event handler.

![event handler](./assets/img/22a/create_event.gif)

The first code we add to the `button_add_click` event handler checks that the user has entered data.

```{code-block} python
:linenos:
:lineno-start: 22
:emphasize-lines: 2-14
  def button_add_click(self, **event_args):
    # validation
    if not self.text_box_subject.text:
      self.label_error.visible = True
      self.label_error.text = "Subject name needed"
    elif not self.text_box_details.text:
      self.label_error.visible = True
      self.label_error.text = "Assessment details needed"
    elif not self.date_picker_start.date:
      self.label_error.visible = True
      self.label_error.text = "Start date needed"
    elif not self.date_picker_due.date:
      self.label_error.visible = True
      self.label_error.text = "Due date needed"
```

```{admonition} Code explaination
:class: notice
- **lines 24 & 27** &rarr; checks that the text box has a value (remember string truthiness)
- **lines 30 & 33** &rarr; checks that a date has been picked (remember object truthiness)
- **lines 25, 28, 31 & 34** &rarr; make the error message visible
- **lines 26, 29, 32 & 35** &rarr; display the error message
```

#### Validation testing

Launch your website and check that it produces an error message for each input element.

### Save Message

Now we want to display a message to the user about what values will be saved.

```{code-block} python
:linenos:
:lineno-start: 22
:emphasize-lines: 15-21
  def button_add_click(self, **event_args):
    # validation
    if not self.text_box_subject.text:
      self.label_error.visible = True
      self.label_error.text = "Subject name needed"
    elif not self.text_box_details.text:
      self.label_error.visible = True
      self.label_error.text = "Assessment details needed"
    elif not self.date_picker_start.date:
      self.label_error.visible = True
      self.label_error.text = "Start date needed"
    elif not self.date_picker_due.date:
      self.label_error.visible = True
      self.label_error.text = "Due date needed"
    else:
      self.label_error.visible = True
      self.subject = self.text_box_subject.text
      self.details = self.text_box_details.text
      self.start = self.date_picker_start.date
      self.due = self.date_picker_due.date
      self.label_error.text = f"{self.subject} {self.details} assessment: {self.start} to {self.due} recorded"
```

```{admonition} Code explaination
:class: notice
- **line 37** &rarr; makes the message available
- **lines 38 - 41** &rarr; reads the values from the form and stores them in the variables
- **line 42** &rarr; creates a string displaying the message and displays it in the error message label.
```

#### Save testing

Launch your website and check if it works

## Clear Form