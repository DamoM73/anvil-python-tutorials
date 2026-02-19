# How websites work

```{topic} In this tutorial you will:
- Understand how computers access websites.
- Learn the difference between frontend (client-side) and backend (server-side) processes.
- Grasp the concept of data flow in web applications.
```

## There is no cloud

We have all heard about 'The Cloud' as a term that refers to where files are located that aren't on your device. It often seems like our computers are connected to some amorphous entity which magically stores and provide information.

![cloud computer](./assets/img/02/cloud_computing.png)

As is often the case, this is a over-simplification for marketing purposes. It allows non-technical people to comprehend concepts enough to use them. In order to create websites, we need a much more technical understanding how the web works. 

## Frontend and Backend

In truth there is no cloud, it is just someone else's computer (a phrase that has inspired many memes). The diagram below is better representation of how you access websites. It is still quite simple, but it will be suffice for our purposes.

![frontend backend](./assets/img/02/frontend_backend.png)

**Frontend:**

- refers to the processes that occur on your device
- also called **client-side**
- is the part of the application that users interact with directly, such as the layout, design, buttons, images, and forms on a website.

**Backend:**

- refers to the processes that occur on the web server
- also called **server-side**
- is remote to the device and accessed through the internet
- handles data processing, storage, and retrieval, ensuring that the frontend receives the necessary data to display to the user

### The web restaurant

The easiest way to think about frontend and backend is if you imagine dining at a restaurant.

The front end is like the waiter, while the backend is like the chef:

- the waiter will provide you with the menu to choose your meal
- the waiter will take your order and give it to the chef
- the chef will use ingredients to complete your order
- the chef will then get the waiter to take your order back to you

Notice that:

- all your interactions with the chef go through the waiter
- the waiter doesn't do any of the cooking
- the chef will need to access stored resources to complete your order

### Real world

In the same way the frontend and backend work together to serve you websites:

- you enter a website address
- the frontend then goes to that address and asks the backend for the website
- the backend uses the stored resources (images, text, code, etc.) to create the requested website
- the backend then sends these to the frontend
- the frontend then displays the website on your screen.
