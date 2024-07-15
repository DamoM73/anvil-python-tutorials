# Add Component Design

```{topic} In this tutorial you will:
- 
```

Now that we have somewhere to store assessment data, we need a way to input it. This will be completed through the **AddComponent**.

## Design

Checking our [design](./03_studyM8_design.md) we will see the layout of this component.

![add component](./assets/img/03/wireframe_add.png)

## The layout

Open the **AddComponent** in the design mode.

### Title

Currently the only element on the component is the title. We'll start by adding an icon to the title.

1. Click on the title
2. Find **Icon** in the **Properties** panel
3. Choose the `fa:plus-square`

![title](./assets/img/22/title.gif)

### Organisational elements

Looking at our design we can see that we will need a **Card** that contains a **Column Panel** so add these to your layout.

![card](./assets/img/22/card.gif)

### Labels and text boxes

Now we need to add the labels and text boxes.

First add the subject elements

1. Add a label
2. Change the **text** to `Subject`
3. Charge its **role** to `input-prompt`
4. Add a text box
5. Change the text box's **name** to `text_box_subject`
6. Change the text box's **placeholder** to `Enter subject name`
7. Change the text box's **align** to right 

![subject](./assets/img/22/subject.gif)

Then add the details elements



```{admonition} Date Formatting
:class: note
asd
```
