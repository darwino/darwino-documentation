# Dynamic Select (Combobox)

The list displayed by a select component can be dynamic, for example coming from a database. Moreover, this content can depend on other values like when comboboxes are linked together.

## Static values
The values are passed through the `options `property. The values are passed as either an array of simple values or an array of object with the `value `and `label` properties. In the first case, the value displayed and the one store in the field are the same, while the second option allows to display a more friendly label to the user.

Example with simple values:

                <div className="col-md-12 col-sm-12">
                    <Field name="select1" component={renderSelect} 
                        label="Select With Static Values - No Label" 
                        disabled={disabled} readOnly={readOnly}
                        options={["M","F"]}
                    />
                </div>
                
Example with custom labels:                

                <div className="col-md-12 col-sm-12">
                    <Field name="select11" component={renderSelect} 
                        label="Select With Static Values" 
                        disabled={disabled} readOnly={readOnly}
                        options={[
                            { value: "M", label: "Male"},
                            { value: "F", label: "Female"}
                        ]}
                    />
                </div>

## Dynamic values
Dyna