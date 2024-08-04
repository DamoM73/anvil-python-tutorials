# Edit Assessments Code

```{topic} In this tutorial you will:
- 
```

## Planning

The code is going to react to two events:

- **button_edit** click
- **button_save** click

So, lets go ahead and create those two event handlers.

Open the **AssessmentPanel** in **Design** mode:

1. click on each button
2. select **on click event**

![event handlers](./assets/img/27/event_handlers.gif)

### Button edit click

When the user clicks the edit button, we want to web app to:

- take the values of the assessment item from this specific panel and write them into the edit elements
- make all the display element invisible
- make all the edit elements visible

Open the **AssessmentPanel** in **Code** mode.

In the **button_edit_click** handler use the code below to copy the values from the display elements.

```{code-block} python
:linenos:
:lineno-start: 27
:emphasize-lines: 2-5
  def button_edit_click(self, **event_args):
    self.text_box_subject.text = self.item["subject"]
    self.text_box_details.text = self.item["details"]
    self.date_picker_start.date = self.item["start_date"]
    self.date_picker_due.date = self.item["due_date"]
```

```{admonition} Code explaination
:class: notice
- **line 28** &rarr; takes **subject** value for specific assessment for this panel and saves it as the text for the subject text box.
- **line 29** &rarr; takes **details** value for specific assessment for this panel and saves it as the text for the details text box.
- **line 30** &rarr; takes **start_date** value for specific assessment for this panel and saves it as the date for the start date picker
- **line 31** &rarr; takes **due_date** value for specific assessment for this panel and saves it as the date for the due date picker
```

Now we need to change the visibility of the elements. To do this we will create a separate function that swaps the visibility of each element, ie. if visibility was `True` it will make it `False`.

We will do this with one function that will work for both event handlers. Add the highlighted code below to the bottom of the **AssessmentPanel** code.

```{code-block} python
:linenos:
:lineno-start: 33
:emphasize-lines: 5-18
  def button_save_click(self, **event_args):
    """This method is called when the button is clicked"""
    pass
    
  def switch_components(self):
    # display elements
    self.label_subject.visible = not self.label_subject.visible
    self.label_details.visible = not self.label_details.visible
    self.label_start.visible = not self.label_start.visible
    self.label_due.visible = not self.label_due.visible
    self.button_edit.visible = not self.button_edit.visible
    
    # edit elements
    self.text_box_subject.visible = not self.text_box_subject.visible
    self.text_box_details.visible = not self.text_box_details.visible
    self.date_picker_start.visible = not self.date_picker_start.visible
    self.date_picker_due.visible = not self.date_picker_due.visible
    self.button_save.visible = not self.button_save.visible
```

```{admonition} Code explaination
:class: notice
- **line 37** &rarr; create the **switch_components** method
- **lines 39 - 43** &rarr; swaps the **visibility** value for each of the display elements
- **lines 46 - 50** &rarr; swaps the **visibility** value for each of the edit elements
```

Finally, to call the **switch_components** method from the **button_edit_click** handler, add the highlighted code.

```{code-block} python
:linenos:
:lineno-start: 27
:emphasize-lines: 6
  def button_edit_click(self, **event_args):
    self.text_box_subject.text = self.item["subject"]
    self.text_box_details.text = self.item["details"]
    self.date_picker_start.date = self.item["start_date"]
    self.date_picker_due.date = self.item["due_date"]
    self.switch_components()
```

```{admonition} Code explaination
:class: notice
- **line 32** &rarr; called the **switch_component** method
```

#### Test the edit button

Launch your website and test if the edit button works. You may need to adjust the width of your columns to make everything fit.

![edit button test](./assets/img/27a/test_edit.gif)

If you have fixed you column widths, then it is time to make the save button work

## Save Button Actions

### Update Assessment Function

### Repopulate Display Components

### Return Visibility
