# Create Update User Function

```{topic} In this tutorial you will:
- 
```

Another problem that was identified through testing, is an issue with the SetDetailsComponent. When a user changes their details, and saves, the AccountComponent continues to display their old details.

## Planning

This problem also stems from the system continuing to use the cached user data. The **button_save_click** hander in the **SetDetailsComponent** writes the new data to the database, but the cached user data doesn't change. When the **AccountComponent** loads it uses the cached (i.e. old) user data.

To resolve this we will need reload the cached user data after the new data is sent to the database.

## Code

Again, we will need to make a new function in the **data_access** module which will:

- write new data to the database
- reload the cached user data

1. Open the  **data_access** module
2. Add the following code to the bottom of the module

```{code-block} python
:linenos:
:lineno-start: 26
:emphasize-lines: 1-7
def update_user(first_name, last_name):
  global __user
  
  print("Writing user details to database")
  anvil.server.call('update_user', first_name, last_name)
  __user = None
  __user = the_user()
```

```{admonition} Code explaination
:class: notice
- **line 26** &rarr; creates the **update_user** function which requires **first_name** and **last_name** to be passed.
- **line 27** &rarr; allows the function to edit the value of `__user`
- **line 29** &rarr; informs the developer that the database is being accessed
- **line 30** &rarr; writes the new user data to the database
- **line 31** &rarr; clears the cached user data
- **line 32** &rarr; caches the new user data
```

Then we need to change the code of the **button_save_click** hander in the **SetDetailsComponent** 

3. Open **SetDetailsComponent**
4. Replace **lines 36 - 38** with the highlighted code below

```{code-block} python
:linenos:
:lineno-start: 23
:emphasize-lines: 14 - 15
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
    data_access.update_user(self.text_box_first_name.text, 
                            self.text_box_last_name.text)

    main_form = get_open_form()
    main_form.switch_component("account")
```

```{admonition} Code explaination
:class: notice
- **lines 36 - 37** &rarr; calls the **update_user** function pass in the values from the first_name and last_name text boxes.
```

## Testing

Launch your web app and change the details of a user. When you click on the save button, the account page should show the new details.

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

def update_user(first_name, last_name):
  global __user
  
  print("Writing user details to database")
  anvil.server.call('update_user', first_name, last_name)
  __user = None
  __user = the_user()
```

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
from .. import data_access


class SetDetailsComponent(SetDetailsComponentTemplate):
  def __init__(self, **properties):
    # Set Form properties and Data Bindings.
    self.init_components(**properties)

    # Any code you write here will run before the form opens.
    user = data_access.the_user()
    if user["first_name"]:
      self.text_box_first_name.text = user["first_name"]
    if user["last_name"]:
      self.text_box_last_name.text = user["last_name"]

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
    data_access.update_user(self.text_box_first_name.text, 
                            self.text_box_last_name.text)

    main_form = get_open_form()
    main_form.switch_component("account")
```
