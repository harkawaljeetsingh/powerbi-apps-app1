let
    // Step 1: Connect to a data source (e.g., Excel, SQL Server, CSV)
    Source = Excel.Workbook(File.Contents("C:\Path\to\Your\File.xlsx"), null, true),
    Sheet1_Sheet = Source{[Item="Sheet1",Kind="Sheet"]}[Data],
    
    // Step 2: Transform and clean the data
    #"Changed Type" = Table.TransformColumnTypes(Sheet1_Sheet,{{"Column1", type text}, {"Column2", type number}}),
    #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Column2] > 100),
    
    // Step 3: Create visualizations
    #"Summarized Data" = Table.Group(#"Filtered Rows", {"Column1"}, {{"Total", each List.Sum([Column2]), type number}}),
    #"Sorted Data" = Table.Sort(#"Summarized Data",{{"Total", Order.Descending}}),
    #"Renamed Columns" = Table.RenameColumns(#"Sorted Data",{{"Column1", "Category"}})
in
    #"Renamed Columns"
