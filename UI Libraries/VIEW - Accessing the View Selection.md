# Accessing the view selection

The grid component has an option to let a user select rows.

# Allowing row selection

Row selection is allowed for grid using the following option:

    selectRows={true}

# Accessing the selection

The selection is an array of selected cursor entries, available through `getSelectedEntries()`.

Here is, for example, how to get the selection as an array of UNIDs. The array of entry is processed to extract the `__meta.unid` for each single entry:

    let sel = this.getGrid().getSelectedEntries().map( e => e.__meta.unid );
