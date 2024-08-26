# Create Logout Function

```{topic} In this tutorial you will:
- 
```

In testing your website in the last tutorial, you would have noticed that when you logout, the website is still acting like your are logged in. This is a problem.

## Planning

Although the website looks like it remains logged in, it is actually logged out. For example, interacting with the database through changing user details would produce an error. The reason for this discrepancy, is that the website continues to use the cached user after the user has logged out. 

To resolve this issue we need the **MainForm** **link_logout_click** handler to:

1. call the `anvil.users.logout()` method
2. clear the cached user value

We will create a new function in the **data_access** module to do this.

## Code

First we need to make the new method in the **data_access** module.

1. Open the **data_access** module
2. Add the following code to the bottom of the module

```{code-block} python
:linenos:
:lineno-start: 21
:emphasize-lines: 1-4
def logout():
  global __user
  __user = None
  anvil.users.logout()
```

```{admonition} Code explaination
:class: notice
- **line 21** &rarr; creates the **logout** function
- **line 22** &rarr; allows the function to edit the value of `__user`
- **line 23** &rarr; sets `__user` to `None`
- **line 24** &rarr; logs out the current user
```

Now we need to change the **link_logout_click** handler so it calls this function.

3. Open the **MainForm** in **Code** mode
4. Change **line 95** to the highlighted code below 

```{code-block} python
:linenos:
:lineno-start: 94
:emphasize-lines: 2
  def link_logout_click(self, **event_args):
    data_access.logout()
    self.switch_component("home")
```

```{admonition} Code explaination
:class: notice
- **line 95** &rarr; calls the **logout** function we just made
```

## Testing

Time to check if that worked.

Launch your website and then logout. You should be taken to the Welcome page.

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
```

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
from .. import data_access


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
      if data_access.the_user():
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

    self.link_register.visible = not data_access.the_user()
    self.link_login.visible = not data_access.the_user()
    self.link_account.visible = data_access.the_user()
    self.link_logout.visible = data_access.the_user()
  
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
    data_access.logout()
    self.switch_component("home")
```