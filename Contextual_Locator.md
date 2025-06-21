<%*
const editor = app.workspace.activeEditor?.editor;
if (!editor) return;

// Remove only the last occurrence of (X) in the title
const fileName = tp.file.title.replace(/\([^()]*\)$/, "").trim();
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

// Construct insertion text, 2 Order to choose
let insertText = "";
if (hierarchy.length > 0) {
    insertText = `${hierarchy.filter(Boolean).reverse().join("--")}--`;
}
insertText += fileName;

/*
let insertText = `${fileName}`;
if (hierarchy.length > 0) {
    insertText += `--${hierarchy.filter(Boolean).join("--")}`;
}
*/

// Remove forbidden filename characters AND emojis/non-standard characters
insertText = insertText
    // Remove basic forbidden filename characters
    .replace(/[*"\\/<>:|?=#^\[\]]/g, "")
    // Remove emojis (comprehensive Unicode ranges)
    .replace(/[\u{1F600}-\u{1F64F}]/gu, "") // Emoticons
    .replace(/[\u{1F300}-\u{1F5FF}]/gu, "") // Misc Symbols and Pictographs
    .replace(/[\u{1F680}-\u{1F6FF}]/gu, "") // Transport and Map
    .replace(/[\u{1F1E0}-\u{1F1FF}]/gu, "") // Regional country flags
    .replace(/[\u{2600}-\u{26FF}]/gu, "")   // Misc symbols
    .replace(/[\u{2700}-\u{27BF}]/gu, "")   // Dingbats
    .replace(/[\u{1F900}-\u{1F9FF}]/gu, "") // Supplemental Symbols and Pictographs
    .replace(/[\u{1FA00}-\u{1FA6F}]/gu, "") // Chess Symbols
    .replace(/[\u{1FA70}-\u{1FAFF}]/gu, "") // Symbols and Pictographs Extended-A
    // Remove other Unicode symbols and special characters
    .replace(/[\u{2000}-\u{206F}]/gu, "")   // General Punctuation
    .replace(/[\u{2070}-\u{209F}]/gu, "")   // Superscripts and Subscripts
    .replace(/[\u{20A0}-\u{20CF}]/gu, "")   // Currency Symbols
    .replace(/[\u{2100}-\u{214F}]/gu, "")   // Letterlike Symbols
    .replace(/[\u{2190}-\u{21FF}]/gu, "")   // Arrows
    .replace(/[\u{2200}-\u{22FF}]/gu, "")   // Mathematical Operators
    .replace(/[\u{2300}-\u{23FF}]/gu, "")   // Miscellaneous Technical
    .replace(/[\u{25A0}-\u{25FF}]/gu, "")   // Geometric Shapes
    .replace(/[\u{2600}-\u{26FF}]/gu, "")   // Miscellaneous Symbols
    // Remove control characters and other problematic Unicode
    .replace(/[\u{0000}-\u{001F}]/gu, "")   // Control characters
    .replace(/[\u{007F}-\u{009F}]/gu, "")   // DEL + C1 control characters
    .replace(/[\u{FE00}-\u{FE0F}]/gu, "")   // Variation Selectors
    .replace(/[\u{200B}-\u{200D}]/gu, "")   // Zero-width characters
    // Clean up multiple spaces and trim
    .replace(/\s+/g, " ")
    .trim();

// Wrap in parentheses
insertText = `(${insertText})`;

// Insert at cursor
editor.replaceRange(insertText, cursorPos);
%>