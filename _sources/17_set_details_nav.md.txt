# SetDetailsComponent Navigation

```{topic} In this tutorial you will:
- Redirect users to a different screen after data is saved.
- Implement code reuse by copying and adapting existing code.
- Debug common runtime and logical errors in a web app.
- Use Anvil commands like `anvil.get_open_form()` for form references.
- Properly import components to ensure code functionality.
```

In last tutorial's test you would notice that once you have saved the details, the **SetDetailsComponent** remained loaded. What we want to do is load the **AccountComponent** so the user can see the current value of their first name and last name.

Admittedly, at the moment, the **AccountComponent** only has a title, but we'll fix that in the next tutorial.

## Plan

The idea is pretty simple, we will need to add the step of loading the **AccountComponent** and change the **lable_title_text** to the end of the **button_save_click** handler. We can copy the appropriate code from **link_account_click** handler in the **MainForm**.

## Code

### Copying the code

First we will copy the text we need.

1. Open the **MainForm** in code mode and find the **link_account_click** handler.
2. Copy the highlighted code

```{code-block} python
:linenos:
:lineno-start: 63
:emphasize-lines: 3 - 6
  def link_account_click(self, **event_args):
    """This method is called when the link is clicked"""
    self.content_panel.clear()
    self.content_panel.add_component(AccountComponent())
    self.label_title.text = self.breadcrumb_stem + " - Account"
    self.set_active_link(("account"))
```

Now we need to paste it into the **button_save_click** handler.

1. Open **SetDetailsComponent** in code mode and find the **button_save_click** handler
2. Paste the code to the bottom of the handler, as below

```{code-block} python
:linenos:
:lineno-start: 17
:emphasize-lines: 18 - 21
  def button_save_click(self, **event_args):
    
    if self.text_box_first_name.text == "":
      self.label_error.text = "First name cannot be blank"
      self.label_error.visible = True
      return

    if self.text_box_last_name.text == "":
      self.label_error.text = "Last name cannot be blank"
      self.label_error.visible = True
      return

    self.label_error.visible = False
    anvil.server.call("update_user", 
                      self.text_box_first_name.text, 
                      self.text_box_last_name.text)

    self.content_panel.clear()
    self.content_panel.add_component(AccountComponent())
    self.label_title.text = self.breadcrumb_stem + " - Account"
    self.set_active_link(("account"))
```

### Testing

Now launch your website for testing:

1. Register another user (you might have to **logout** first).
2. Enter their first name and last name
3. Click **Save Details**
4. **WHAT JUST HAPPENED?**

You would have just got the following **runtime error**:

![content panel error](./assets/img/17/content_panel_error.png)

```{admonition} Types of Programming Errors
:class: note
Programming errors fall into three categories

1. **Syntax Errors**
   - Occur when the code doesn't follow the rules of the programming language.
   - Detected by the compiler or interpreter before running the code.
   - Example: Missing a closing parenthesis.

2. **Runtime Errors**
   - Occur during the execution of the program.
   - Cause the program to crash or produce unexpected results.
   - Example: Dividing by zero.

3. **Logic Errors**
   - Occur when the code runs but produces incorrect results.
   - Due to flaws in the logic of the code.
   - Example: Incorrect formula in a calculation.
```

#### AttributeError analysis

Well if says the error occurred at **line 34** so lets start there:

`self.content_panel.clear()` &rarr; clear content_panel in self (this component).

You can see that the **self** is the problem. It refers to the component the code resides in (in this case the **SetDetailsComponent**), when we actually want to refer to **MainForm** where **content_panel** resides.

So we need a way to refer to the **MainForm** from with in the **SetDetailsComponent** code. Luckily Anvil provide a command to do this &rarr; `anvil.get_open_form()`

#### Using the open form

The Anvil command `anvil.get_open_form()` returns the current open form, in our case it will be **MainForm**. This means we can get the **MainForm**, store it in a variable (`main_form`). This will enable fixing the code we just pasted by replacing `self` with `main_form`. 

Make the changes highlighted below.

```{code-block} python
:linenos:
:lineno-start: 17
:emphasize-lines: 18 - 22
  def button_save_click(self, **event_args):
    
    if self.text_box_first_name.text == "":
      self.label_error.text = "First name cannot be blank"
      self.label_error.visible = True
      return

    if self.text_box_last_name.text == "":
      self.label_error.text = "Last name cannot be blank"
      self.label_error.visible = True
      return

    self.label_error.visible = False
    anvil.server.call("update_user", 
                      self.text_box_first_name.text, 
                      self.text_box_last_name.text)

    main_form = get_open_form()
    main_form.content_panel.clear()
    main_form.content_panel.add_component(AccountComponent())
    main_form.label_title.text = main_form.breadcrumb_stem + " - Account"
    main_form.set_active_link(("account"))
```

### Test again

Lets test again. The erroneous code came after writing the details to the **User table**, so you will need to delete those details to test again. then launch your website.

Now launch your website for testing:

1. Register another user
2. Enter their first name and last name
3. Click **Save Details**
4. **WHAT AGAIN???**

We still have an error. But look at the error closely, it's a different, so that's progress.

![account component error](./assets/img/17/accountcomponent_error.png)

#### NameError analysis

This error points to **line 36**.

`main_form.content_panel.add_component(AccountComponent())` &rarr; in the **content_panel** of the **MainForm** load the **AccountComponent**

It's complaining because there is no **AccountComponent** in the **SetDetailsComponent**. We resolved this issue in the **MainForm** by importing the **AccountComponent** using the following line:

`from ..AccountComponent import AccountComponent`

So we need to add this to the import statement at the top of **SetDetailsComponent**.

#### Importing AccountComponent

Open the **SetDetailsComponent** in code mode an then add the highlighted code below:

```{code-block} python
:linenos:
:lineno-start: 1
:emphasize-lines: 8
from ._anvil_designer import SetDetailsComponentTemplate
from anvil import *
import anvil.server
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.users
from ..AccountComponent import AccountComponent
```

### Third test lucky

Again, delete the user you just added and launch your website.

1. Register another user
2. Enter their first name and last name
3. Click **Save Details**
4. You should now be on the **AccountComponent**, stop your website and check that the user details have been saved to the **User table**

Now that is all working, we have to add details to the **AccountComponent**.

## Final code state

By the end of this tutorial your code should be the same as below:

### Final SetDetailsComponent

```{code-block} python
:linenos:
from ._anvil_designer import SetDetailsComponentTemplate
from anvil import *
import anvil.server
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.users
from ..AccountComponent import AccountComponent


class SetDetailsComponent(SetDetailsComponentTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)

    # Any code you write here will run before the form opens.

  def button_save_click(self, **event_args):
    
    if self.text_box_first_name.text == "":
      self.label_error.text = "First name cannot be blank"
      self.label_error.visible = True
      return

    if self.text_box_last_name.text == "":
      self.label_error.text = "Last name cannot be blank"
      self.label_error.visible = True
      return

    self.label_error.visible = False
    anvil.server.call("update_user", 
                      self.text_box_first_name.text, 
                      self.text_box_last_name.text)

    main_form = get_open_form()
    main_form.content_panel.clear()
    main_form.content_panel.add_component(AccountComponent())
    main_form.label_title.text = main_form.breadcrumb_stem + " - Account"
    main_form.set_active_link(("account"))
```