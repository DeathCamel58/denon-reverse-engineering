# Controller Assignments

Engine uses `/usr/Engine/AssignmentFiles/PresetAssignmentFiles/[HARDWARE_ID]/[HARDWARE_ID]_*.qml` to describe all the
buttons, LEDs, and such on the unit. These buttons are mapped to various features.

For example, the `JP11_Controller_Assignments.qml` includes this by default:

```
PerformanceModes {
    ledType: LedType.RGB
    modesModel: ListModel {
        ListElement {
            note: 11
            view: 'CUES'
        }
        ListElement {
            note: 12
            view: 'LOOPS'
            altView: 'AUTO'
        }
        ListElement {
            note: 13
            view: 'ROLL'
            altView: 'SAMPLER'
            shiftView: 'SAMPLER'
        }
    }
}
```

This shows the mods that a button is mapped to with a standard press (`view`), the mode that it switches to when pressed
again (`altView`), and the mode that it switches to when `SHIFT` is held while the button is pressed (`shiftView`).

In this example, slicer mode (which isn't a feature of the `JP11`) can be added to the `LOOP` button by changing the `ListElement` for the button to:
```
ListElement {
    note: 12
    view: 'LOOPS'
    shiftView: 'SLICER'
}
```