# User Home Component

```{topic} In this tutorial you will:
- 
```

Now we can turn to the **HomeComponent**. This component lists all the current assessments and their respective details. Lets plan this out

## Planning

This is where we start to get tricky. Remember this web app is dynamic, that means how it looks is dependent on the stored data. In the case, the number of rows in our **HomeComponent** is dependent on the number of relevant assessments stored in our **Assessments** table.

There are two things we need to achieve this:

1. We need to retrieve a list of assessments data items from the **Assessments** table.
2. We need a layout element that will display the rows from that list.

To retrieve the list we will create a new function called **get_assessments** in the **assessment_service** module.

To display the list we will use a layout element called a **Repeating Panel**. **Repeating Panels** display a list of items in a repeated pattern. When it is connected to a list of data items, the panel repeats for each data item. It is probably easier to understand in practice, so let's get started.

## Layout

Open the **HomeComponent** in the **Design** mode.

First step:

- add the `fa:home` icon to the title
- add a card under the title

![adding card](./assets/img/25/card.gif)



## Code


## Get Assessment Function

## Repeating Panel

### Add Panel

### Connect to item

### Fix Formatting