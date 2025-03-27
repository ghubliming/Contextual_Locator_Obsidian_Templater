<%*
const editor = app.workspace.activeEditor?.editor;
if (!editor) return;

const fileName = tp.file.title.replace(/\(.*?\)/g, "").trim(); // Remove brackets
const cursorPos = editor.getCursor();
const content = editor.getValue();

// Extract all headings
const headings = [...content.matchAll(/^(#{1,6})\s(.+)/gm)].map(match => ({
    level: match[1].length, 
    text: match[2], 
    index: match.index 
}));

// Identify heading hierarchy before cursor
let hierarchy = [];
for (let i = 0; i < headings.length; i++) {
    if (headings[i].index < editor.posToOffset(cursorPos)) {
        // Ensure that same-level headings overwrite previous ones
        hierarchy[headings[i].level - 1] = headings[i].text;
        hierarchy = hierarchy.slice(0, headings[i].level); // Remove deeper levels
    } else {
        break;
    }
}

// Construct insertion text
let insertText = `${fileName}`;
if (hierarchy.length > 0) {
    insertText += `--${hierarchy.filter(Boolean).join("--")}`;
}

// Remove forbidden filename characters
insertText = insertText.replace(/[*"\\/<>:|?]/g, "");

// Wrap in parentheses
insertText = `(${insertText})`;

// Insert at cursor
editor.replaceRange(insertText, cursorPos);
%>
