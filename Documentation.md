# **FIFA 21 DATA CLEANING**

![](header1-03.jpg)
## Introduction
This documentation outlines the data cleaning process for the **FIFA 21 dataset**, which was obtained from [Kaggle.](https://www.kaggle.com/datasets/yagunnersya/fifa-21-messy-raw-dataset-for-cleaning-exploring) The raw data file was named "fifa21 raw data v2.csv" and was provided as part of a **Data Cleaning Challenge** for data analysts of all levels. The data cleaning process was completed using Power Query, a powerful data transformation tool. The purpose of this project was to clean and prepare the dataset for further analysis.

## Data Description
The FIFA 21 dataset contains 18,979 rows and 77 columns. The dataset includes a variety of data types, including whole numbers, text, and dates. The columns contain a range of information related to FIFA player statistics, such as player name, club, nationality, position, earnings, performance metrics and so on.

## Data Cleaning Process
Using M language and a host of tools available in Power Query Editor, I was able to perform the following transformations to the data set;

### - Photo URL Correction
During the data cleaning process, an issue was identified with the "Photo URL" column. The column contained URLs to each player's photo, but it was noticed that the URLs contained an error that caused the photos to be unavailable.

For example, the original URL for one player was:
https://cdn.sofifa.com/players/158/023/21_60.png
This URL contained an error that prevented the photo from loading correctly. To fix this issue, a transformation was applied to the dataset to correct the URLs.

![](PhotoURL.jpg)

By applying this transformation, the "Photo URL" column was cleaned and standardized to contain the correct URLs, which improved the accuracy and completeness of the data.
https://cdn.sofifa.net/players/158/023/21_240.png 

### - String/Text Cleaning:
One of the issues identified during the data cleaning process was with the "Club" column. The column contained some dirty data that needed to be cleaned. To clean the data in this column, the Power Query function Text.Clean was used. By applying this transformation, any extraneous whitespace, line breaks, or other non-printable characters were removed from the "Club" column, resulting in a cleaner, more consistent dataset. _[see preview below;]_ 👇

![](clean2.jpg)

### - Columns OVA, POT & BOV Transformation
During the data cleaning process, it was noticed that the columns labelled "OVA", "POT", and "PAC" contained values that were measured as percentages, but were entered as text data types. To correct this issue, a transformation was applied to the dataset to convert the values to a percentage format and change the data type to percentage.
_[see preview below;]_ 👇

![](ova-pot-bov.jpg)

By applying this transformation, the "OVA", "POT", and "PAC" columns were cleaned and standardized to a consistent percentage format, which improved the accuracy and usefulness of the data.

### - Contract Column Transfromation

The "Contract" column contained contract start year, contract end year (separated by "~"), as well as the values "Free" (for players not on contract) and "Loan Date" (for players on loan). To improve the accuracy and usefulness of the data, two transformations were applied to this column.

First, a new column called "Agreement" was created to indicate the type of agreement for each player.

![](Agreement.jpg)

Second, a new column called "Contract Duration" was created to calculate the duration of each player's contract in years. This was done using the following transformation:

```
if Text.Contains([Contract], "~")
then Number.From( Text.AfterDelimiter([Contract], "~"))- Number.From( Text.BeforeDelimiter([Contract], "~"))
else [Agreement]
```

This transformation used the Text.BeforeDelimiter and Text.AfterDelimiter functions to extract the contract start year and contract end year from each row in the "Contract" column, and then calculated the difference between them to determine the duration of the contract in years. If the "Agreement" column indicated that the player was not on a contract ("Free" or "Loan"), the corresponding row in the "Agreement" column is returned.

![](Contract.jpg)

By applying these transformations, the "Contract" column was cleaned and standardized to include information on the type of agreement for each player, as well as the duration of their contract (if applicable), which improved the accuracy and usefulness of the data.

### - Height and Weight columns Transformation

The "Height" column and "Weight" column contained data that needed to be converted to a consistent unit of measurement.

#### Height column

Some entries were in centimeters (cm), while others were in feet and inches (ft/in). The following steps were taken to address this issue:

1. Identified all entries in ft/in format using the Text.Contains function.
2. Separated the feet and inches values using the Text.BetweenDelimiters function.
3. Converted the feet value to centimeters by multiplying by 30.48.
4. Converted the inches value to centimeters by multiplying by 2.54.
5. Added the converted feet and inches values to get the final height in centimeters (cm).

#### Weight column

Some entries were in kilograms (kg), while others were in pounds (lbs). The following steps were taken to address this issue:

1. Identified all entries in lbs format using the Text.Contains function.
2. Converted the lbs value to kilograms by dividing by 2.205.

After these transformations, both the Height and Weight columns were consistent and measured in cm and kg respectively.

The following Power Query M code was used to carry out the transformations:

#### Height:

```
if Text.Contains([Height], "cm")
then Number.From(Text.BeforeDelimiter([Height],"cm"))
else Number.Round(Number.FromText(Text.BeforeDelimiter([Height],"'"))*30.48 + Number.FromText(Text.AfterDelimiter([Height],"'"))*2.54)
```

#### Weight:

```

```

![](hw.jpg)

### Value, Wages and Release Clause Columns Transformation

Date in these columns contained the Euro (€) sign and values were entered in different formats. The following steps were taken to clean the data:

1. The Euro sign was removed from the Value, Wages, and Release Clause columns using the Text.Replace function.
2. The values were then converted to numeric format using the Number.FromText function.T
3. The values were converted to their respective units, i.e., M for million and K for thousand, as follows:
4. Values in millions were represented by M (e.g., 1.6M instead of 1600000).
5. Values in thousands were represented by K (e.g., 2K instead of 2000).
6. Finally, the values were converted to Dollar ($)

The following code was used to implement these changes:

#### Value:

```
let 
ReplaceEuro1 = Text.Replace([Value], "€", ""),
Convert1 = if Text.Contains(ReplaceEuro1, "M")
           then Number.FromText(Text.BeforeDelimiter(ReplaceEuro1, "M")) * 1000000
           else if Text.Contains(ReplaceEuro1, "K")
           then Number.FromText(Text.BeforeDelimiter(ReplaceEuro1, "K")) * 1000
           else Number.FromText(ReplaceEuro1)

in
Convert1 *1.58
```

#### Wage:

```
let 
ReplaceEuro = Text.Replace([Wage], "€", ""),
Convert = if Text.Contains(ReplaceEuro, "K") then Number.FromText(Text.BeforeDelimiter(ReplaceEuro, "K")) * 1000 
          else Number.FromText(ReplaceEuro)

in 
Convert *1.58
```

#### Release Clause:

```
let 
ReplaceEuro2 = Text.Replace([Release Clause], "€", ""),
Convert2 = if Text.Contains(ReplaceEuro2, "M")
           then Number.FromText(Text.BeforeDelimiter(ReplaceEuro2, "M")) * 1000000
           else if Text.Contains(ReplaceEuro2, "K") then Number.FromText(Text.BeforeDelimiter(ReplaceEuro2, "K")) * 1000 else Number.FromText(ReplaceEuro2)

in
Convert2 *1.58
```
![](Wages1-04.jpg)

### - W/F, SM IR Columns Transformation

The columns W/F, SM IR Columns had the star "★" sign added to the data. I simply replaced the sign with blank "" and changed the data type to whole number.

![](wf-sm-ir.jpg)

### - Hits Column Transformation
I worked on the "Hits" column, which had values represented in thousands with the letter "K" (e.g. 1.6K instead of 1600). To convert these values to whole numbers, I removed the "K" and multiplied the remaining value by 1000. The resulting values were then updated in the column.

![](Hits.jpg)

## Conclusion
After completing the data cleaning process, the dataset was thoroughly reviewed and analyzed to draw insights into the state of the FIFA21 player data.

The dataset contained 18,979 rows and 77 columns with various data types, including whole numbers, text, and dates.

Several columns had issues that required attention, including the "Club," "Contract," "Photo URL," "OVA," "POT," "Height," "Weight" and other columns.

Specific data cleaning activities were performed on each of these columns, including using the Text.Clean function to fix issues in the "Club" column, creating new columns to categorize agreement type and calculate contract duration in the "Contract" column, and converting measurements in the "Height" and "Weight" columns to a consistent unit of measurement.

After completing the cleaning process, several insights could be drawn from the dataset, including the distribution of player attributes, such as overall rating, potential rating, and market value.

Overall, the insights drawn from the project highlight the importance of thorough data cleaning processes in ensuring the accuracy and reliability of datasets for further analysis. The cleaned dataset can now be used for further analysis, modeling, and visualization.

![](ty.jpg)
Thank you for reading
