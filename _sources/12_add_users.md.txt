# Adding Users

```{topic} In this tutorial you will:
- Set up user management
- Add custom user fields
```

Anvil provides a range of useful features. One of these features is user management. The user management tool  simplifies the process of handling user signups, logins, and permissions within an Anvil application. It provides built-in functions to manage user authentication and maintain user data securely.

In this tutorial we will set up the user management system. In the next tutorial we will incorporate it's features into our website.

## Setting up user management

To setup our user management click on the **Add** button on the sidebar

![add](./assets/img/12/add.png)

Then choose **Users**

![users](./assets/img/12/users.png)

Notice that there is a new User icon in the sidebar. This is how you can get back to this page.

![users icon](./assets/img/12/user_icon.png)

On the Users page:

1. Select the checkbox for **Remember login between sessions** &rarr; will save you login in every time you launch your website.
   - the default 30 days will be fine.
2. Select the checkbox for **Allow visitors to sign up** &rarr; will allow you to use the registration system.
3. Deselect the checkbox for **Confirm email address before users can log in** &rarr; will allow you to use bogus emails for development.

![user settings](./assets/img/12/user_settings.png)

That's all our setting done.

You can see that there are a heap more user settings. You may choose to turn some of these on in the future, but the current settings will make development run more smoothly.

## Adding Fields

Down the bottom of the Users page, you will notice the users table. This table will store the details of all the users who register on your website. Most of the columns as self evident, but there are two more that we want to add:

- first_name
- last_name

Scroll the table all the way to the right then click on the plus sign to add a new column.

In the **Add a Column** dialogue:

1. Name the column **first_name**
2. Choose **Text** as the Column Type
3. Then click **OK**

![first name](./assets/img/12/first_name.png)

Now add another column called **last_name**

![last_name](./assets/img/12/last_name.png)

That's it. Your user management tool is now ready.
