# Adjust Assessments Display

```{topic} In this tutorial you will:
- 
```

We can enter assessment details and we can view them. We even have a check box on the **AssessmentPanel** that allows the user to tick whether they have completed an assessment.


Now we can display all our assessments, but do we really want to display all assessments. What about assessments that have been completed?

In this tutorial you will adjust the **HomeComponent** so that it only displays the assessments that are still outstanding.

## Change assessments displayed

Since the information on the repeating panel is derived from it's items list, we will need to change the item list. The item list comes from the **get_assessment** function in the **assessment_service**, so we need to make changes there.

### get_assessment code

Open the **get_assessment** function in the **assessment_service** and make the highlighted change tot he code:

```{code-block} python
:linenos:
:lineno-start: 25
:emphasize-lines: 3
  return app_tables.assessments.search(tables.order_by('due_date'),
                                       user=user,
                                       completed=False)
```

```{admonition} Code explaination
:class: notice
- **`completed=False`** &rarr; just like line 26, this is a restriction on the rows that will be retrieved. Only those assessments that have not been completed will be retrieved.
```

### Testing

At this stage, no 

## Event from checkbox
