# System Generated Tasks 

- A `Task` resource must be initialized with the desired data and sent to Wizard's `task_manager` workflow. 
- Information shown in the **Task Card** view and the **Task Details** view, will be represented by the `input` property on the `Task` resource. Detailed structure below.
- Outcomes (actions) that you can take on a System-Generated Task will be represented by the `output` property on the `Task` resource. Detailed structure below.
- Related resources can also be included on the `Task` object, this is represented by the `identifier` property.

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
  - `value` must follow one of the formats below 
  - `text` property is the label of the field

### valueString
  - This is used for simple label-value cases 
  - `"\\s*(\\S|\\s)*"`
### valueDateTime
  - Used for date and time values. 
  -  `"([0-9]([0-9]([0-9][1-9]|[1-9]0)|[1-9]00)|[1-9]000)(-(0[1-9]|1[0-2])(-(0[1-9]|[1-2][0-9]|3[0-1])(T([01][0-9]|2[0-3]):[0-5][0-9]:([0-5][0-9]|60)(\\.[0-9]+)?(Z|(\\+|-)((0[0-9]|1[0-3]):[0-5][0-9]|14:00)))?)?)?"` 
### valueCoding 
  ```
  {
    system: "css-class",
    code: "important",
    display: "Surgery",
  }
  ```
  - `system` This property is used to signifiy that the `code` property of this `input` should also apply a css `class`
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
- If using the Three-Column-View please send in the `value` in the following JSON format.
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
- Properties on each object will be the subsection 
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
    title: "BUTTON_LABEL",
  }
  }
}
```
### type
  - `type` is a required CodeableConcept
  - The `text` property inside the `type`'s CodeableConcept is an optional css `class` that you may want to apply to your button. If the class does not exist in rogue_ui then the UI member for your team will have to create the desired aesthetics.
### valueRelatedArtifact
  - `valueRelatedArtifact` will contain all the information needed to support the Outcome Button
  - `id` represents the type of button, position of button, and position inside a group if applicable -> `buttonType-buttonIndex-groupIndex`. If the button is not a group, please put a `groupIndex` of 0. 
  - `buttonType` - `"button" | "buttonGroup" | "ellipsis"`
  - `buttonIndex` - Buttons will be ordered from left -> right. There is also a maximum of 4 buttons. If you need more outcomes please consider either a `buttonGroup` or `ellipsis`
  - `display` used to applay a css `variant` for the Button. A `variant` is different than a regular `class` because it is a set of styles. Refer to rogue_storybook for details on each `variant`
  - `document.title` The label of the button
  - `document.data` The mutation to execute. All the variables **MUST** be supplied. The UI will parse the data, and simply call
#### data
```
[
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
    ]
```
- Despite this property being an array, we currently only support one action. This behavior may be imporved in future sprints
- `query` The query/mutation that will be called. It must follow the above format with the mutation name declared as well as all variables
- `variables` **Exactly** what you wish to be sent with the mutation. There will no other variables aside from what you include here. 
- The UI will run the code as follows 
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
  valueCode: "close" | "launch-sidebar" | "redirect"
}
```

## Identifiers 
 - Used to include a Resource that may be related to the Task. For example, a `DocumentReference` is listed as an `identifier` for the Finalize_Letter task. An `Encounter` is a likely case for an identifier. 
 ```
 {
   system: "DocumentReference",
   value: "a629d149-d862-422d-b072-5d828efc13ba"
 }
 ```
 - `system` - The Fhir resource you wish to reference 
 - `value` - The id of the resource 


## Mock Task Resource 
```
{
  id: 'd0b05cf4-70a9-4289-ac0c-0e153f2292ef',
  meta: {
    id: '87534e81-0f69-4ea2-83eb-7288adabb91b',
    profile: [
      '3.0/StructureDefinition/profile-Task.workflowInitiatedTask',
    ],
    tag: [
      {
        system: 'CodeSystem/carecloud-meta-tag-scope',
        code: 'Organization/70e21d61-c19f-4d03-b4fa-e05e1a7f1fa1',
      },
    ],
  },
  resourceType: 'Task',
  identifier: [
    {
      system: 'DocumentReference',
      value: 'a629d149-d862-422d-b072-5d828efc13ba',
    },
  ],
  status: 'completed',
  intent: 'PROPOSAL',
  priority: 'ROUTINE',
  code: {
    coding: [
      {
        id: '7f6c8b30-d0eb-4e44-a2d7-1238a08b1f97',
        system: 'CodeSystem/carecloud-task-code',
        code: 'finalize-letter',
        display: 'Finalize Letter',
      },
    ],
    text: 'Finalize Letter',
  },
  description: 'Finalize a patient letter',
  authoredOn: '2019-11-13T16:05:21.935Z',
  lastModified: '2019-11-13T16:05:21.935Z',
  input: [
    {
      id: '0c292197-0178-4d6b-aaa8-508308db1689',
      type: {
        coding: [
          {
            id: '6e6c06c3-1969-4998-b7c4-4d20ba9b44d3',
            system: 'CodeSystem/carecloud-task-input-types',
            code: 'patient',
          },
        ],
        text: 'Patient',
      },
      valueReference: {
        id: 'f02995a1-6bac-4300-ad3b-6ab1788586b7',
        reference: 'Patient/bf1b7262-9d75-4367-9f0d-ea69b910f2ac',
        display: 'Patient Link',
      },
    },
    {
      id: '074ce32c-4547-42d6-9073-777c8abbda57',
      type: {
        coding: [
          {
            id: '0887e31c-6c80-4045-9e5a-c381b1dc6838',
            system: 'CodeSystem/carecloud-task-input-types',
            code: 'task-card',
          },
        ],
        text: 'Patient',
      },
      valueReference: {
        id: 'de8d17ce-70ae-46c9-b8b8-5bcc442564ab',
        reference: 'Patient/bf1b7262-9d75-4367-9f0d-ea69b910f2ac',
        display: 'Patient Link',
      },
    },
    {
      id: '093431ac-142c-4ed7-a865-fbcad68ce276',
      type: {
        coding: [
          {
            id: '8d903062-3962-48cd-9f5c-48cd8bf318df',
            system: 'CodeSystem/carecloud-task-input-types',
            code: 'task-card',
          },
        ],
        text: 'Title',
      },
      valueString: 'Physician-to-SSA Letter (DEMO)',
    },
    {
      id: '1d30eda2-572b-4e0b-9f88-ee839bd03bf3',
      type: {
        coding: [
          {
            id: '17960d61-5e86-465a-9094-ef52b441c94c',
            system: 'CodeSystem/carecloud-task-input-types',
            code: 'task-card',
          },
        ],
        text: 'Authored On',
      },
      valueDateTime: '2019-11-07T00:03:17.776Z',
    },
    {
      id: 'baa3f276-a6eb-492f-bbe6-69c4dae8199f',
      type: {
        coding: [
          {
            id: 'ce0b6665-6208-4c73-a560-005ae5e3a9be',
            system: 'CodeSystem/carecloud-task-input-types',
            code: 'task-card',
          },
        ],
        text: 'Author',
      },
      valueReference: {
        id: 'c6e75f82-a15e-40af-9a25-5d714b83ecaa',
        reference: 'Practitioner/9c4c9665-7f58-4f9c-9b0f-66d4e684b635',
        display: 'Teresa Plaz',
      },
    },
    {
      id: 'f3f8a094-1d3a-404c-bc66-bb68bbab2c78',
      type: {
        coding: [
          {
            id: 'bdc7da39-bc96-439c-846f-6ad2a1035f43',
            system: 'CodeSystem/carecloud-task-input-types',
            code: 'task-details',
          },
        ],
        text: 'PdfPreview',
      },
      valueString: 'DocumentReference/a629d149-d862-422d-b072-5d828efc13ba',
    },
  ],
  output: [
    {
      id: '3ce4259b-1d18-41b9-946e-56e060ada92f',
      extension: [
        {
          id: 'd1e3409f-5db2-4b46-9c52-508adaf15196',
          url: '3.0/StructureDefinition/extension-Task.postAction',
          valueCode: 'close',
        },
      ],
      type: {
        coding: [
          {
            id: '78dbe8a4-6d5e-4a0b-8b78-1dfd2e31c11e',
            system: 'CodeSystem/carecloud-task-input-types',
            code: 'task-outcome',
          },
        ],
      },
      valueRelatedArtifact: {
        id: 'button-1-0',
        type: 'composed-of',
        display: 'raised',
        document: {
          contentType: 'plain/text',
          data: 'W3siYWN0aW9ucyI6eyJvcGVyYXRpb25OYW1lIjoiRXhlY3V0ZUNvbmR1Y3RvcldvcmtmbG93IiwicXVlcnkiOiJtdXRhdGlvbiBFeGVjdXRlQ29uZHVjdG9yV29ya2Zsb3coJG5hbWU6IFN0cmluZyEsICRkYXRhOiBKU09OISkge2V4ZWN1dGVDb25kdWN0b3JXb3JrZmxvdyhuYW1lOiAkbmFtZSwgZGF0YTogJGRhdGEpfSIsInZhcmlhYmxlcyI6eyJuYW1lIjoiY29tcGxldGVfbGV0dGVyIiwiZGF0YSI6eyJzZXNzaW9uRGF0YSI6eyJkb2N1bWVudFJlZmVyZW5jZVJlZmVyZW5jZSI6IkRvY3VtZW50UmVmZXJlbmNlLzE5MjIyOTcwLTc3OWItNDdhZC05ZGIxLTIzMjFjOThmNWIyNSIsInJlc291cmNlSWQiOiIxOTIyMjk3MC03NzliLTQ3YWQtOWRiMS0yMzIxYzk4ZjViMjUifX19fX1d',
          title: 'Finalize Letter',
        },
      },
    },
  ],
}
```
