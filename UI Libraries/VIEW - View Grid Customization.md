# View grid customization

The Darwino view grid is based is based on the react-data-grid component, with a few enhancements.

## Custom column rendering
The content of a column is generally the row content of a JSON field, converted to a string.
But this content can be formatte using a grid column property:

    {name: "Date", key: "Date", resizable:true, formatter: DateFormatter, width:150},

This formatter is a function that returns a piece of markup. In the example bellow, it uses `FormattedDate` from `react-intl` to format the date:

    export const DateFormatter = function(props) {
      const date = props.value;
      return (
        <div>
          <FormattedDate value={date}/>, <FormattedTime value={date}/>
        </div>
      )
    }

A formatter can also return more complex markup, including images. 
        
See: `ByDate` for the date formatting
See: `ByState` for displaying an image in a cell
