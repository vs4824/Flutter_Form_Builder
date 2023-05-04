# Flutter Form Builder

This package helps in creation of data collection forms in Flutter by removing the boilerplate needed to build a form, validate fields, react to changes and collect final user input.

Also included are common ready-made form input fields for FormBuilder. This gives you a convenient way of adding common ready-made input fields instead of creating your own FormBuilderField from scratch.

## Features

1. Create a form with several type of inputs

2. Get values from form by easy way

3. Apply validators to inputs fields

4. React to form fields changes and validations.

## Inputs

The currently supported fields include:

1. FormBuilderCheckbox - Single checkbox field

2. FormBuilderCheckboxGroup - List of checkboxes for multiple selection

3. FormBuilderChoiceChip - Creates a chip that acts like a radio button.

4. FormBuilderDateRangePicker - For selection of a range of dates

5. FormBuilderDateTimePicker - For Date, Time and DateTime input

6. FormBuilderDropdown - Used to select one value from a list as a Dropdown

7. FormBuilderFilterChip - Creates a chip that acts like a checkbox

8. FormBuilderRadioGroup - Used to select one value from a list of Radio Widgets

9. FormBuilderRangeSlider - Used to select a range from a range of values

10. FormBuilderSegmentedControl - For selection of a value using the CupertinoSegmentedControl widget as an input

11. FormBuilderSlider - For selection of a numerical value on a slider

12. FormBuilderSwitch - On/Off switch field

13. FormBuilderTextField - A Material Design text field input

### Parameters

In order to create an input field in the form, along with the label, and any applicable validation, there are several attributes that are supported by all types of inputs namely:

## Use

### Setup

No specific setup required: only install the dependency and use :)

### Basic use

   ```
   final _formKey = GlobalKey<FormBuilderState>();

FormBuilder(
    key: _formKey,
    child:  FormBuilderTextField(
        name: 'text',
        onChanged: (val) {
            print(val); // Print the text value write into TextField
        },
    ),
)
```

### Specific uses

Building your own custom field

To build your own field within a FormBuilder, we use FormBuilderField which will require that you define your own field. Read this article for step-by-step instructions on how to build your own custom field.

   ```
   var options = ["Option 1", "Option 2", "Option 3"];
   ```

   ```
   FormBuilderField(
  name: "name",
  validator: FormBuilderValidators.compose([
# Flutter Form Builder
    FormBuilderValidators.required(),
  ]),
  builder: (FormFieldState<dynamic> field) {
    return InputDecorator(
      decoration: InputDecoration(
        labelText: "Select option",
        contentPadding:
            EdgeInsets.only(top: 10.0, bottom: 0.0),
        border: InputBorder.none,
        errorText: field.errorText,
      ),
      child: Container(
        height: 200,
        child: CupertinoPicker(
          itemExtent: 30,
          children: options.map((c) => Text(c)).toList(),
          onSelectedItemChanged: (index) {
            field.didChange(options[index]);
          },
        ),
      ),
    );
  },
),
```

Programmatically changing field value

You can either change the value of one field at a time like so:

   ```
   _formKey.currentState.fields['color_picker'].didChange(Colors.black);
   ```

Or multiple fields value like so:

   ```
   _formKey.currentState.patchValue({
  'age': '50',
  'slider': 6.7,
  'filter_chip': ['Test 1'],
  'choice_chip': 'Test 2',
  'rate': 4,
  'chips_test': [
    Contact(
        'Andrew', 
        'stock@man.com', 
        'https://d2gg9evh47fn9z.cloudfront.net/800px_COLOURBOX4057996.jpg',
    ),
  ],
});
   ```

Programmatically inducing an error

Using form state key or field state key

   ```
   final _formKey = GlobalKey<FormBuilderState>();
final _emailFieldKey = GlobalKey<FormBuilderFieldState>();
...
FormBuilder(
  key: _formKey,
  child: Column(
    children: [
      FormBuilderTextField(
        key: _emailFieldKey,
        name: 'email',
        decoration: const InputDecoration(labelText: 'Email'),
        validator: FormBuilderValidators.compose([
          FormBuilderValidators.required(),
          FormBuilderValidators.email(),
        ]),
      ),
      ElevatedButton(
        child: const Text('Submit'),
        onPressed: () async {
          if(await checkIfEmailExists()){
            // Either invalidate using Form Key
            _formKey.currentState?.fields['email']?.invalidate('Email already taken');
            // OR invalidate using Field Key
            _emailFieldKey.currentState?.invalidate('Email already taken');
          }
        },
      ),
    ],
  ),
),
   ```

When use invalidate and validate methods, can use two optional parameters configure the behavior when invalidate field or form, like focus or auto scroll. Take a look on method documentation for more details

Using InputDecoration.errorText

Declare a variable to hold your error:

   ```
   String _emailError;
   ```

Use the variable as the errorText within InputDecoration

   ```
   FormBuilderTextField(
  name: 'email',
  decoration: InputDecoration(
    labelText: 'Email',
    errorText: _emailError,
  ),
  validator: FormBuilderValidators.compose([
      FormBuilderValidators.required(),
      FormBuilderValidators.email(),
  ]),
),
```

Set the error text

   ```
   RaisedButton(
  child: Text('Submit'),
  onPressed: () async {
    setState(() => _emailError = null);
    if(await checkIfEmailExists()){
      setState(() => _emailError = 'Email already taken.');
    }
  },
),
   ```

Conditional validation

You can also validate a field based on the value of another field

   ```
   FormBuilderRadioGroup(
  decoration: InputDecoration(labelText: 'My best language'),
  name: 'my_language',
  validator: FormBuilderValidators.required(),
  options: [
    'Dart',
    'Kotlin',
    'Java',
    'Swift',
    'Objective-C',
    'Other'
  ]
    .map((lang) => FormBuilderFieldOption(value: lang))
    .toList(growable: false),
  ),
  FormBuilderTextField(
    name: 'specify',
    decoration:
        InputDecoration(labelText: 'If Other, please specify'),
    validator: (val) {
      if (_formKey.currentState.fields['my_language']?.value ==
              'Other' &&
          (val == null || val.isEmpty)) {
        return 'Kindly specify your language';
      }
      return null;
    },
  ),
  ```

Implement reset, clear or other button into field

If you can add some button to reset specific field, can use the decoration parameter like this:

   ```
   List<String> genderOptions = ['Male', 'Female', 'Other'];

FormBuilderDropdown<String>(
  name: 'gender',
  decoration: InputDecoration(
    labelText: 'Gender',
    initialValue: 'Male',
    suffix: IconButton(
      icon: const Icon(Icons.close),
      onPressed: () {
        _formKey.currentState!.fields['gender']
            ?.reset();
      },
    ),
    hintText: 'Select Gender',
  ),
  items: genderOptions
      .map((gender) => DropdownMenuItem(
            alignment: AlignmentDirectional.center,
            value: gender,
            child: Text(gender),
          ))
      .toList(),
),
   ```

Or if is allowed by the field, set a value like this:

   ```
   FormBuilderTextField(
  name: 'age',
  decoration: InputDecoration(
    labelText: 'Age',
    suffixIcon: IconButton(
      icon: const Icon(Icons.plus_one),
      onPressed: () {
        _formKey.currentState!.fields['age']
            ?.didChange('14');
      },
    ),
  ),
  initialValue: '13',
  keyboardType: TextInputType.number,
),
   ```