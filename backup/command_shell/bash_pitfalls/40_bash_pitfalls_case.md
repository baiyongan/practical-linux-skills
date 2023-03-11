# Bash Pitfalls Case 40
## On UTF-8 and Byte-Order Marks (BOM)

In general: Unix UTF-8 text does not use BOM. The encoding of plain text is determined by the locale or by mime types or other metadata. While the presence of a BOM would not normally damage a UTF-8 document meant only for reading by humans, it is problematic (often syntactically illegal) in any text file meant to be interpreted by automated processes such as scripts, source code, configuration files, and so on. Files starting with BOM should be considered equally foreign as those with MS-DOS linebreaks.

In shell scripting: 'Where UTF-8 is used transparently in 8-bit environments, the use of a BOM will interfere with any protocol or file format that expects specific ASCII characters at the beginning, such as the use of "#!" of at the beginning of Unix shell scripts.' http://unicode.org/faq/utf_bom.html#bom5