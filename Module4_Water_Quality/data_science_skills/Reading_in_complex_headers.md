# Checklist for reading in a data file to R

* Open up raw data in an editor
* In Rstudio you can use File -> Open File
	* You can use TextEditor or something like that
	* If you use Excel do not save the file because it can change column formats
* Check for comment lines in the a header.  Are there a set number of lines to skip?
* What is used to separate (delimitate) values?  Comma, space, tab?
* What is the expected class of each column? Numeric, character, factor? datetime?
* Select the appropriate function for reading in the file based on the delimitating character (i.e., read_csv) and use the correct number of skips.
* Confirm that your variable column names and classes are as expected
	* Did you skip too many lines, thus skip data?
	* Did a column assigned the incorrect class?
	* Are NAs correctly assigned?
