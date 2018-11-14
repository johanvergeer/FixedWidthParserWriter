## FixedWidthParserWriter
Library (C# .NET) for Parsing(Reading) & Writing fixed-width/flat data files (.txt or other).<br>
It is targeting NetStandard 2.0 so it can be used on project targeting NetCore(2.0+) or NetFramework(4.6.1+).

Available on [![NuGet](https://img.shields.io/badge/NuGet-1.0.0-blue.svg)](https://www.nuget.org/packages/FixedWidthParserWriter/) latest version.<br>
Package manager console command for installation: *Install-Package FixedWidthParserWriter*

Thare are 2 main types of usage:<br>

### 1. Data in LineFields
First is regular flat data file (**record per Line**), for example:
```
No |         Description         |Unit| Qty |   Price    |Disc%|   Amount   |
  1.Laptop Dell xps13             Pcs       1       856.00  0.00       856.00
  2.Monitor Asus 32''             Pcs       2       568.00  0.00      1136.00
```
For parsing/writing we make a model that inherits 'FixedWidthDataLine' which Properties have `[FixedWidthLineField]` Att:
```C#
public class InvoiceItem : FixedWidthDataLine<InvoiceItem>
{
   
    [FixedWidthLineField(Start = 1, Length = 3)]
    public int Number { get; set; }

    [FixedWidthLineField(Start = 4, Length = 1)]
    public string SeparatorNumDesc { get; set; } = ".";

    [FixedWidthLineField(Start = 5, Length = 30)]
    public string Description { get; set; }

    [FixedWidthLineField(Start = 35, Length = 5)]
    public string Unit { get; set; }

    [FixedWidthLineField(Start = 40, Length = 6)]
    public int Quantity { get; set; }

    [FixedWidthLineField(Start = 46, Length = 13)]
    public decimal Price { get; set; }

    [FixedWidthLineField(Start = 59, Length = 6)]
    public decimal Discount { get; set; }

    [FixedWidthLineField(Start = 65, Length = 13)]
    public decimal Amount => Quantity * Price;
}
```
`[FixedWidthLineField]` has following parameters that can be configured for each Property:
- *Start*
- *Length*
- *Format* (Defaults per data type: DateTime=""yyyyMMdd"", Int="", Decimal="0.00", Boolean="1;;0")
- *Pad* (Defaults per data group type: CharacterSeparator=' ', NumericSeparator='0')
- *PadSide* {Right, Left} (Defaults: NonNumberic=PadSide.Left, Numeric=PadSide.Right)
- *StructureTypeId* (used when having multiple files with different structure or format for same data)

### 2. Data in FileFields
Second usage is when one data record is in diferent rows at defined positions (**record per File**), for example:
```
SoftysTech LCC
Local Street NN
______________________________________________________________________________

Invoice Date: 2018-10-30               Buyer:   SysCompanik
Due Date:     2018-11-15               Address: Some Location

                              INVOICE no. 0169/18
...                                                                           
...                                                                           
------------------------------------------------------------------------------
                                                                     1,192.00 

Date: 2018-10-31                                             Financial Manager
                                                                      John Doe
```


```C#
public class Invoice : FixedWidthDataFile<Invoice>
{
    [FixedWidthFileField(Line = 1)]
    public string CompanyName { get; set; }

    [FixedWidthFileField(Line = 2)]
    public string CompanyAddress { get; set; }

    [FixedWidthFileField(Line = 5, Start = 15, Length = 25, Format = "yyyy-MM-dd")]
    public DateTime Date { get; set; }

    [FixedWidthFileField(Line = 6, Start = 15, Length = 25, Format = "yyyy-MM-dd")]
    public DateTime DueDate { get; set; }

    [FixedWidthFileField(Line = 5, Start = 49)]
    public string BuyerName { get; set; }

    [FixedWidthFileField(Line = 6, Start = 49)]
    public string BuyerAddress { get; set; }

    [FixedWidthFileField(Line = 8, Start = 43)]
    public string InvoiceNumber { get; set; }

    [FixedWidthFileField(Line = -4, Length = 77, Pad = ' ', Format = "0,000.00")]
    public decimal AmountTotal { get; set; }

    [FixedWidthFileField(Line = -2, Start = 7, Length = 10)]
    public DateTime DateCreated { get; set; }

    [FixedWidthFileField(Line = -2, Start = 17, Length = 62, PadSide = PadSide.Left)]
    public string SignatoryTitle { get; set; }

    [FixedWidthFileField(Line = -1, Length = 78, PadSide = PadSide.Left)]
    public string SignatureName { get; set; }
}
```


In situation where many same type properties have Format different from default one, instead of setting that format individualy for each one, it is possible to override default format for certain data type in that class:
```C#
    public class InvoiceDefaultFormat : DefaultFormat
    {
        public override string DateTimeFormat { get; set; } = "yyyy-MM-dd";
    }
    
    public class Invoice : FixedWidthDataFile<Invoice>
    {
        public override void SetFormatAndPad()
        {
            Format = new InvoiceDefaultFormat();
        }
        
        [FixedWidthFileField(Line = 1)]
        public string CompanyName { get; set; }

        [FixedWidthFileField(Line = 2)]
        public string CompanyAddress { get; set; }

        // Format set on class with custom DefaultFormat so not required on each Attribute of DateTime Property
        [FixedWidthFileField(Line = 5, Start = 15, Length = 25/*, Format = "yyyy-MM-dd"*/)]
        public DateTime Date { get; set; }
        
        /* ... Other Properties */
    }
```

## Contributing

If you find this project useful you can mark it by leaving a Github **\*Star**.</br>

Please read [CONTRIBUTING](CONTRIBUTING.md) for details on code of conduct, and the process for submitting pull requests.

[![NuGet](https://img.shields.io/npm/l/express.svg)](https://github.com/borisdj/FixedWidthParserWriter/blob/master/LICENSE)

## Contact
Want to contact us for Hire (Development & Consulting):</br>
[www.codis.tech](http://www.codis.tech)
