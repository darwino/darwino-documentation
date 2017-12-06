# Export to CSV

The value returned by queries through REST services is generally a JSON array. But the runtime also provides an export capability that returns the data using different formats, like CSV.

See the `/export` service: https://playground.darwino.com/playground.nsf/OpenApiExplorer.xsp#openApi=Json_Store_Query_Database


Here is, for example, how to compose a URL that will return the content of a query as CSV:

    export(format) {
        const url = this.createJstoreCursor()
          .queryParams({
              format, 
              columns: this.getColumnNames().join(','),
              titles: this.getColumnTitles().join(',')
          })
          .computeUrl('/entries/export')
        window.open(url); 
    }
    
Such a URL can contain any Darwino cursor property, like query, data extraction, ...

See: `AllContacts.jsx`