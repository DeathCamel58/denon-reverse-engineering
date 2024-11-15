# Controller Assignments

Engine uses `/usr/Engine/AssignmentFiles/PresetAssignmentFiles/[HARDWARE_ID]/[HARDWARE_ID]_*.qml` to describe all the
buttons, LEDs, and such on the unit. These buttons are mapped to various features.

## Controller Performance Modes

The `[HARDWARE_ID]_Controller_Assignments.qml` specifies the button mapping for each supported controller mode.

### Performance Modes List

| Performance Mode | `JC11`             | `JC16`             | `JC20`             | `JP07`             | `JP08`             | `JP11`             | `JP13`             | `JP14`             | `JP20`             | `JP21`             | `JP22`             | `NH08`             | `NH09`             | `NH10`             | `RMZ2`             |
|------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|
| `AUTO`           | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| `CUES`           | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| `FIXED`          | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:                | :white_check_mark: | :white_check_mark: | :x:                | :white_check_mark: | :white_check_mark: | :x:                | :x:                | :x:                | :white_check_mark: |
| `LOOPS`          | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:                | :white_check_mark: | :white_check_mark: |
| `ROLL`           | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:                | :white_check_mark: | :white_check_mark: |
| `SAMPLER`        | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| `SIMPLE_ROLL`    | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :white_check_mark: | :x:                | :x:                |
| `SLICER`         | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :x:                | :white_check_mark: | :white_check_mark: | :x:                | :white_check_mark: | :white_check_mark: | :x:                | :x:                | :x:                | :white_check_mark: |
| `STEMS`          | :white_check_mark: | :x:                | :white_check_mark: | :x:                | :x:                | :x:                | :x:                | :x:                | :x:                | :white_check_mark: | :white_check_mark: | :x:                | :x:                | :x:                | :x:                |

### Modifying

To change the compatible performance modes, modify your device's QML at `/usr/Engine/AssignmentFiles/PresetAssignmentFiles/[HARDWARE_ID]/[HARDWARE_ID]_Controller_Assignments.qml`


On the `JP11`, the controller assignment contains:

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

This shows the modes that a button is mapped to with a standard press (`view`), the mode that it switches to when pressed
again (`altView`), and the mode that it switches to when `SHIFT` is held while the button is pressed (`shiftView`).

In this example, slicer mode (which isn't a feature of the `JP11`) can be added to the `LOOP` button by changing the
`ListElement` for the button to:

```
ListElement {
    note: 12
    view: 'LOOPS'
    shiftView: 'SLICER'
}
```

Slicer enabling [test apps](HARDWARE-ID-Test-App.md) for the `JP11`, `JP11S`, `JP20`, `NH08`, `NH09`, and `NH10` can be
downloaded here: <resource src="TestAppsCatalog-Slicer.zip"/>
