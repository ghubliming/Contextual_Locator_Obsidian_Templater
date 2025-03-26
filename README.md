# Contextual_Locator: Obsidian Heading Path Inserter

This is a Templater script for Obsidian that inserts the current file's location path (filename + heading hierarchy) at the cursor position in the active note.

## Features
- Inserts the current file name and heading hierarchy path at the cursor
- Formats output as `(filename--heading1--heading2--...)`
- Automatically detects heading hierarchy up to cursor position
- Removes invalid filename characters
- Works with Markdown headings (#, ##, ###, etc.)

## Usage
1. Install the [Templater plugin](https://github.com/SilentVoid13/Templater) in Obsidian
2. Add this script to your Templater templates folder
3. Trigger the template in an open note to insert the path at your cursor position

The script will:
- Get the current file name (removing existing parenthetical content)
- Analyze all headings before the cursor
- Build a hierarchy path joining filename and headings with `--`
- Insert the result in parentheses at the cursor

## Example
In a file named "Meeting Notes" with content:
```
# Project Planning
## Sprint 1
### Tasks
[Cursor here]
```
Running the script would insert: `(Meeting Notes--Project Planning--Sprint 1--Tasks)`

## Recommended Companion
For the best experience, use this alongside [autorename_file_name_with_path_Obsidian_Templater](https://github.com/ghubliming/autorename_file_name_with_path_Obsidian_Templater). Together they enable a Notion-like workflow for:
- Creating new files with automatic naming based on path
- Linking files using consistent path references

## Requirements
- Obsidian
- Templater plugin
- Active editor window

## Notes
- The script only processes headings before the cursor position
- Invalid filename characters (*"\\/<>:|?) are automatically removed
- Works with heading levels 1-6 (h1-h6)