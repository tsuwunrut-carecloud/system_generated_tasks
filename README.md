# System Generated Tasks 

- A `Task` resource must be initialized with the desired data and sent to Wizard's `task_manager` workflow. 
- Information shown in the *Task Card* view and the *Task Details* view, will be represented by the `input` property on the `Task` resource. Detailed structure below.
- Outcomes (actions) that you can take on a System-Generated Task will be represented by the `output` property on the `Task` resource. Detailed structure below.

## Inputs
 - Used to determine what data is displayed on each Task Card, and which `view` for the Task Details 
 ```
  {
    type: {
      text: "\\s*(\\S|\\s)*"
      coding: [
        {
          system: "CodeSystem/carecloud-task-input-types",
          "code": "task-card" | "task-details" | "patient",
        }
      ],
    }
    value: valueString | valueCoding | valueDateTime | valueReference
  }
 ```
  - `type` is a CodeableConcept and is required
  - `value` must following one of the formats below 
  - `text` property is the label of the field ex.    field: label

### valueString
  - This is used for simple label-value cases 
  - "\\s*(\\S|\\s)*"
### valueDateTime
  - Used for date and time values. 
  -  "([0-9]([0-9]([0-9][1-9]|[1-9]0)|[1-9]00)|[1-9]000)(-(0[1-9]|1[0-2])(-(0[1-9]|[1-2][0-9]|3[0-1])(T([01][0-9]|2[0-3]):[0-5][0-9]:([0-5][0-9]|60)(\\.[0-9]+)?(Z|(\\+|-)((0[0-9]|1[0-3]):[0-5][0-9]|14:00)))?)?)?" 
### valueCoding 
  ```
  {
    system: "css-class",
    code: "important",
    display: "Surgery",
  }
  ```
  - `system` This property is used to signifiy that the `code` property should be applied to a css `class`
  - `code` The css class to apply. If the class or styles does not exist in rogue_ui then the UI member of this team will have to add the desired aesthetics 
  - `display` The text that will be shown for the field 
### valueReference 
```
  {
    display: "German Pabon",
    reference: "Practitioner/$id"
  }
```
-  A `valueReference` is used to either provide a link to a resource either for navigation or queries
- An example route would be `/patient/$id/patient-center`

## View Inputs 
  ```
  {
    type: {
      text: "DESIRED_VIEW"
      coding: [
        {
          system: "CodeSystem/carecloud-task-input-types",
          "code": "task-details",
        }
      ],
    }
    valueString: "STRINGIFIED_JSON_DATA"
  }
  ```
- This is used when a Custom View is needed for the Task Details. A Custom View is always needed when anything beyond the Three-Column-View is required. The UI member for that team will have to construct the desired view within Inbox_UI. 
- If you would like to use the default Three-Column-View, please send in a `text` property of `"ColumnView"`. We are open to suggestions for a better name.
- If using the Three-Column-View please send in the `value` this following JSON format.
```

      {
         data: [
           {
             'Refill Details': [{ label, value }, { label, value }, { label, value }]
           },
           {
             'Original Prescription': [{ label, value }, { label, value }, { label, value }]
           },
           {
             'Prescriber': [{ label, value }, { label, value }],
             'Pharmacy': [{ label, value }, { label, value }],
             'Patient Details': [{ label, value }, { label, value }]
           }
         ]
       }
```
- Each object in the `data` array will be a column
- Each property on the object will be the subsection 
- Refer to the mocks for visual representation
- https://projects.invisionapp.com/d/main?origin=v7#/console/14437659/392862697/preview?scrollOffset=7047.5


## Outputs 
- This is used to determine the outcomes you can take on the Task.
- These outcomes can trigger a query, mutation, launch a sidebar form, or navigate you from one place in the application to another
```
{
  type: {
    coding: [
      {
        system: "CodeSystem/carecloud-task-input-types",
        code: "task-outcome",
        text: "\\s*(\\S|\\s)*",
      }
    ]
  }
  valueRelatedArtifact: {
  id: "buttonGroup-1-0"
  type: "composed-of",
  display: "inverted" | "flat" | "outlined" | "raised" | "fab",
  document: {
    contentType: "plain/text",
    data: [
      {
        actions: {
          operationName: 'ExecuteConductorWorkflow',
          query: 'mutation ExecuteConductorWorkflow($name: String!, $data: JSON!) {executeConductorWorkflow(name: $name, data: $data)}',
          variables: {
            name: 'complete_letter',
            data: {
              sessionData: {
                documentReferenceReference: 'DocumentReference/$id',
                resourceId: '231fd06f-8a7c-48d5-89f6-3a3f441dbed4',
              },
            },
          },
        },
      },
    ],
    title: "Complete Letter",
  }
  }
}
```
- `type` is a required CodeableConcept
- `valueRelatedArtifact` will contain all the information needed to support the Outcome Button
- `valueRelatedArtifact.id` represents the type of button, position of button, and position inside a group if applicable -> buttonType-buttonIndex-groupIndex. If the button is not a group, please put a grupIndex of 0.
- `valueRelatedArtifact.display` used to applay a css `variant` for the Button. 
- `valueRelatedArtifact.document.title` The label of the button
- `valueRelatedArtifact.document.data` The mutation to execute. All the variables *MUST* be supplied. The UI will parse the data, and simply call
```
mutate({
  mutation: gql(actions.query)
  variables: actions.variables
})
```
- If your mutation should close the task, open a sidebar, or navigate the user to a different location, this must be provided in the extension. An example extension would be 
```
{
  url: "3.0/StructureDefinition/extension-Task.postAction",
  valueCode: "close"
}
```

