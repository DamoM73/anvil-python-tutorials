# Prevent Unauthorised Access

```{topic} In this tutorial you will:
- 
```

Presently our web app is pretty much open to everyone. Even if you are not logged in, you can view the Home, Add and Calendar pages. Although these pages don't hold sensitive private information, the functionality of these pages depend up a user being logged in:

- **HomeComponent** &rarr; displays the user's assessment
- **CalendarComponent** &rarr; displays the user's assessment timeline
- **AddComponent** &rarr; adds the user's assessment details

This means that if there is no user logged in, then these components will not function correctly. In this tutorial, we will fix that problem.

## Planning

For the **AddComponent** and the **CalendarComponent** we will add a second card with and error message. Then, when the component is loaded, either the default card or the error card will be displayed.

When it comes to home we will choose between loading the **HomeComponent** or the **WelcomeComponent**, depending upon the user login status.

## Layout

### AddComponent Layout

Open the **AddComponent** in **Design** mode.

1. Choose the current card
2. Change it's name from **self.card_1** to **self.card_details**

![card details](./assets/img/24/add_card_details.gif)

3. Add a new card between **self.card_details** and **self.label_message** (pay close attention to the brown lines)
4. Name the new card **self.card_error**

![card error](./assets/img/24/add_card_error.gif)


5. Add a label to the **self.card_error**
6. Change the **text** to `Please login to access this feature`
7. Change **foreground** to `#ff0000`
8. Change **role** to your desired formatting
9. Change **icon** to `fa:warning`
10. Change **align** to `center`

![login error](./assets/img/24/error_message.gif)

### CalendarComponent Layout

We haven't added anything to the **CalendarComponent**, so we will need to add two cards:

- call the first one **self.card_details**
- call the second one **self.card_error**

![calendar](./assets/img/24/calendar.gif)

Leave **self.card_details** blank, and then change **self.card_error** to be the same as **AddComponent**.

### Home Page Layout

We don't have to change the layout of the **HomeComponent** as we will simply load the **WelcomeComponent** instead. We will worry about the **welcomeComponent** layout later on.

## Code

### AddComponent code

The AddComponent code will simply check if someone is logged in and adjust the visibility of **self.card_details** and **self.card_error** accordingly.

Open **AddComponent** in **Code** mode.

In the `__init__` method add the highlighted code:

```{code-block} python
:linenos:
:lineno-start: 11
:emphasize-lines: 11 - 18
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)
    self.subject = ""
    self.details = ""
    self.start = None
    self.due = None

    # Any code you write here will run before the form opens.
    self.label_message.visible = False
    if anvil.users.get_user():
      self.card_details.visible = True
      self.card_error.visible = False
      self.button_add.visible = True
    else:
      self.card_details.visible = False
      self.card_error.visible = True
      self.button_add.visible = False
```

```{admonition} Code explaination
:class: notice
- **line 21** &rarr; checks if there is a user currently logged in
- **lines 22 - 24** &rarr; sets the UI to display the elements appropriate for a logged in user
- **lines 26 - 28** &rarr; sets the UI to display the elements appropriate for no logged in user
```

### CalendarComponent code

Use the same logic to display the correct elements in the **CalendarComponent**.

In **CalendarComponents** `__init__` method, add the code below:

```{code-block} python
:linenos:
:lineno-start: 11
:emphasize-lines: 6 - 11
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)

    # Any code you write here will run before the form opens.
    if anvil.users.get_user():
      self.card_details.visible = True
      self.card_error.visible = False
    else:
      self.card_details.visible = False
      self.card_error.visible = True
```

### Home page code

The Home page code is trickier, since it involves two components. Luckily we have centralised all component loading into the **switch_component** method in the **MainForm**. We can check for user status there and load the **HomeComponent** is a user is logged in or load the **WelcomeComponent** if there is no user logged in.

Open the **MainForm** in **Code** mode.

First, if we are going to load the **WelcomeComponent**, we need to import it. Add the highlighted code to the import statement.

```{code-block} python
:linenos:
:lineno-start: 1
:emphasize-lines: 13
from ._anvil_designer import MainFormTemplate
from anvil import *
import anvil.server
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.users
from ..HomeComponent import HomeComponent
from ..CalendarComponent import CalendarComponent
from ..AddComponent import AddComponent
from ..AccountComponent import AccountComponent
from ..SetDetailsComponent import SetDetailsComponent
from ..WelcomeComponent import WelcomeComponent
```

Now in the **switch_component** method add the following code:

```{code-block} python
:linenos:
:lineno-start: 25
:emphasize-lines: 4-7
  def switch_component(self, state):
    # set state
    if state == "home":
      if anvil.users.get_user():
        cmpt = HomeComponent()
      else:
        cmpt = WelcomeComponent()
      breadcrumb = self.breadcrumb_stem
```


- **line 27** &rarr; note this code is only executed when the **home** page is being loaded
- **line 28** &rarr; checks if there is a user currently logged in
- **line 29** &rarr; loads the **HomeComponent**
- **line 30 & 31** &rarr; loads the **WelcomeComponent** is no user is logged in

### Testing

Launch your website. Your should be logged in. Make sure that you can access all three of the pages we have worked on:

- Home
- Add
- Calendar

Then logout and check all three pages again.

![testing](./assets/img/24/testing.gif)

## Final code state

By the end of this tutorial your code should be the same as below:

### Final MainForm

```{code-block} python
:linenos:
from ._anvil_designer import MainFormTemplate
from anvil import *
import anvil.server
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.users
from ..HomeComponent import HomeComponent
from ..CalendarComponent import CalendarComponent
from ..AddComponent import AddComponent
from ..AccountComponent import AccountComponent
from ..SetDetailsComponent import SetDetailsComponent
from ..WelcomeComponent import WelcomeComponent


class MainForm(MainFormTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)
    self.breadcrumb_stem = self.label_title.text

    # Any code you write here will run before the form opens.
    self.switch_component("home")

  def switch_component(self, state):
    # set state
    if state == "home":
      if anvil.users.get_user():
        cmpt = HomeComponent()
      else:
        cmpt = WelcomeComponent()
      breadcrumb = self.breadcrumb_stem
    elif state == "account":
      cmpt = AccountComponent()
      breadcrumb = self.breadcrumb_stem + " - Account"
    elif state == "add":
      cmpt = AddComponent()
      breadcrumb = self.breadcrumb_stem + " - Add"
    elif state == "calendar":
      cmpt = CalendarComponent()
      breadcrumb = self.breadcrumb_stem + " - Calendar"
    elif state == "details":
      cmpt = SetDetailsComponent()
      breadcrumb = self.breadcrumb_stem + " - Account - Set Details"
    
    # execution
    self.content_panel.clear()
    self.content_panel.add_component(cmpt)
    self.label_title.text = breadcrumb
    self.set_active_link(state)
  
  def set_active_link(self, state):
    if state == "home":
      self.link_home.role = "selected"
    else:
      self.link_home.role = None
    if state == "add":
      self.link_add.role = "selected"
    else:
      self.link_add.role = None
    if state == "calendar":
      self.link_calendar.role = "selected"
    else:
      self.link_calendar.role = None

    self.link_register.visible = not anvil.users.get_user()
    self.link_login.visible = not anvil.users.get_user()
    self.link_account.visible = anvil.users.get_user()
    self.link_logout.visible = anvil.users.get_user()
  
  # --- link handlers
  def link_home_click(self, **event_args):
    self.switch_component("home")

  def link_calendar_click(self, **event_args):
    self.switch_component("calendar")

  def link_add_click(self, **event_args):
    self.switch_component("add")

  def link_account_click(self, **event_args):
    """This method is called when the link is clicked"""
    self.switch_component("account")

  def link_register_click(self, **event_args):
    anvil.users.signup_with_form(allow_cancel=True)
    self.switch_component("details")

  def link_login_click(self, **event_args):
    anvil.users.login_with_form(allow_cancel=True)
    self.switch_component("home")

  def link_logout_click(self, **event_args):
    anvil.users.logout()
    self.switch_component("home")
```

### Final AddComponent

```{code-block} python
:linenos:
from ._anvil_designer import AddComponentTemplate
from anvil import *
import anvil.server
import anvil.tables as tables
import anvil.tables.query as q
from anvil.tables import app_tables
import anvil.users


class AddComponent(AddComponentTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)
    self.subject = ""
    self.details = ""
    self.start = None
    self.due = None

    # Any code you write here will run before the form opens.
    self.label_message.visible = False
    if anvil.users.get_user():
      self.card_details.visible = True
      self.card_error.visible = False
      self.button_add.visible = True
    else:
      self.card_details.visible = False
      self.card_error.visible = True
      self.button_add.visible = False

  def button_add_click(self, **event_args):
    # validation
    if not self.text_box_subject.text:
      self.display_error("Subject name needed")
    elif not self.text_box_details.text:
      self.display_error("Assessment details needed")
    elif not self.date_picker_start.date:
      self.display_error("Start date needed")
    elif not self.date_picker_due.date:
      self.display_error("Due date needed")
    else:
      self.subject = self.text_box_subject.text
      self.details = self.text_box_details.text
      self.start = self.date_picker_start.date
      self.due = self.date_picker_due.date
      self.display_save(f"{self.subject} {self.details} assessment: {self.start} to {self.due} recorded")
      anvil.server.call('add_assessment', self.subject, self.details, self.start, self.due)
      self.reset_form()

  def display_error(self, message):
    self.label_message.visible = True
    self.label_message.foreground = "#ff0000"
    self.label_message.icon = "fa:exclamation-triangle"
    self.label_message.bold = True
    self.label_message.text = message

  def display_save(self, message):
    self.label_message.visible = True
    self.label_message.foreground = "#000000"
    self.label_message.icon = "fa:save"
    self.label_message.bold = False
    self.label_message.text = message

  def reset_form(self):
    self.subject = ""
    self.details = ""
    self.start = None
    self.due = None
    self.text_box_subject.text = ""
    self.text_box_details.text = ""
    self.date_picker_start.date = None
    self.date_picker_due.date = None
```

### Finial CalendarComponent

```{code-block} python
:linenos:
from ._anvil_designer import CalendarComponentTemplate
from anvil import *
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
    else:
      self.card_details.visible = False
      self.card_error.visible = True
```