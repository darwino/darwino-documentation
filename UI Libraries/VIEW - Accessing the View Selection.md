# Defining the view columns

A Darwino grid is displaying the data in a tabular form, using columns. The column can be

## Extracting the column data
If nothing is specified in the query itself, then the whole content of the documents is extracted and returned as part of the data. This might be ok, but a developer might want to define what data should specifically be retrieved:
1. To minimize the amount of data transferred from the server to the client
2. To calculate some result on the server side, and make them simple to consume by the client

When columns are being defined, then the returned data is a JSON object with one entry per column.
Columns are defined in the query object through the `extract` attribute. The attribute key is the column name, while the value is the expression to be evaluated for that columns. The expressions are evaluated for every single document:

    {
      "extract": {
        "Industry": "industry",
        "State": {$USState: "$state"},
        "Name": "name",
        "form": "form"
      },
    }
A simple name indicates the name of a field at the root of the document.

## Using Notes/Domino @formula language
Darwino is equiped with a @formula interpreter that can be used to calculate data based on the JSON document. $atFormala is the function used to evaluate an actual formula:

    "CommonName": {
      "$atFormula": '@If(LastName="";"Misc";"  "+@Left(@ProperCase(LastName);1))' 
    },
