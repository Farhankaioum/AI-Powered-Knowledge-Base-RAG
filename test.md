# Simple Trello MVP - Workshop Guide

## 1. Project Requirements
**Objective**: Build a simplified version of Trello (a kanban board application) where users can:
- Create multiple boards
- Add lists to each board
- Add cards to lists
- Edit card names
- Drag and drop cards between lists
- Persist data using browser's localStorage
- Have a clean, user-friendly interface

## 2. Requirement Analysis & Task List

### Core Features:
1. **Board Management**
   - Create new boards
   - View all boards
   - Navigate to board details

2. **List Management**
   - Add new lists to a board
   - Edit list names inline
   - Display lists horizontally

3. **Card Management**
   - Add new cards to lists
   - Edit card names via double-click
   - Delete cards (implied by drag/drop removal)
   - Drag and drop cards between lists

4. **Data Persistence**
   - Save all data to localStorage
   - Load data on page refresh

5. **User Interface**
   - Clean, responsive design
   - Clear navigation between views
   - Visual feedback for drag and drop

### Technical Tasks:
- HTML structure for both views
- CSS styling for boards, lists, and cards
- JavaScript for:
  - State management
  - DOM manipulation
  - Event handling
  - Drag and drop API
  - LocalStorage operations

## 3. Project Setup

### Tools Needed:
1. **Code Editor**: VS Code, Sublime Text, or any text editor
2. **Browser**: Chrome/Firefox (for DevTools)
3. **File Structure**:
   ```
   trello-mvp/
   ├── index.html
   ├── styles.css
   └── app.js
   ```

### Kanban Board Setup (Trello-style planning):
Create a Trello board with these columns:
1. **To Do**: Initial features to implement
2. **In Progress**: Currently working on
3. **Testing**: Features to test
4. **Done**: Completed features

**Initial Cards for "To Do" column:**
- [ ] Create HTML structure for board list view
- [ ] Create HTML structure for board detail view
- [ ] Basic CSS styling
- [ ] JavaScript: Board creation and rendering
- [ ] JavaScript: List creation and rendering
- [ ] JavaScript: Card creation and rendering
- [ ] Implement drag and drop
- [ ] Implement localStorage
- [ ] Add edit functionality
- [ ] Polish UI and fix bugs

# Simple Trello MVP - Feature-Wise Step-by-Step Guide

## **Feature 1: Project Setup & Basic Structure**

### HTML (index.html - Part 1)
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Simple Trello MVP</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>Simple Trello MVP</h1>
  </header>
  <main>
    <!-- We'll add content here step by step -->
  </main>
  <script src="app.js"></script>
</body>
</html>
```

### CSS (styles.css - Part 1)
```css
/* Reset and basic styling */
body {
    font-family: Inter, Arial, sans-serif;
    background: #f5f6fa;
    margin: 0;
    padding: 0;
}

header {
    background: #2563eb;
    color: #fff;
    padding: 10px 20px;
}

header h1 {
    font-size: 18px;
    margin: 0;
}

main {
    padding: 20px;
}
```

### JavaScript (app.js - Part 1)
```javascript
// Just an empty file for now
console.log("Trello MVP started!");
```

**✅ FEATURE 1 COMPLETE**: Basic project structure ready.

---

## **Feature 2: Create and Display Boards**

### HTML Update
```html
<main>
  <div id="page-boards">
    <h2>Create Board</h2>
    <div style="display:flex;gap:8px;margin-bottom:20px">
      <input id="board-name" placeholder="Board name" />
      <button id="add-board">Add</button>
    </div>
    <div class="board-list" id="board-list">
      <!-- Boards will appear here dynamically -->
    </div>
  </div>
</main>
```

### CSS Update
```css
/* Board styling */
.board-list {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
}

.board-item {
    background: #fff;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    padding: 20px;
    border-radius: 8px;
    width: 200px;
    cursor: pointer;
    text-align: center;
}

.board-item:hover {
    background: #f0f4ff;
}

/* Input and button styling */
input, button {
    padding: 8px 10px;
    border-radius: 6px;
    border: 1px solid #ddd;
    outline: none;
}

button {
    background: #2563eb;
    color: #fff;
    cursor: pointer;
    border: none;
}
```

### JavaScript Update
```javascript
// Feature 2: Board Management
const boardListEl = document.getElementById('board-list');
const addBoardBtn = document.getElementById('add-board');
const boardNameEl = document.getElementById('board-name');

// In-memory storage
let boards = [];

// Generate unique ID
function uid() {
    return Date.now().toString(36) + Math.random().toString(36).slice(2, 7);
}

// Render all boards
function renderBoards() {
    boardListEl.innerHTML = '';
    
    boards.forEach(board => {
        const boardDiv = document.createElement('div');
        boardDiv.className = 'board-item';
        boardDiv.textContent = board.name;
        boardListEl.appendChild(boardDiv);
    });
}

// Add new board
addBoardBtn.onclick = () => {
    const name = boardNameEl.value.trim();
    if (!name) {
        alert('Please enter a board name');
        return;
    }
    
    boards.push({
        id: uid(),
        name: name,
        lists: []
    });
    
    boardNameEl.value = '';
    renderBoards();
};

// Initialize
renderBoards();
```

**✅ FEATURE 2 COMPLETE**: Can create and display boards.

---

## **Feature 3: Board Details View & Navigation**

### HTML Update
```html
<header>
  <h1>Simple Trello MVP</h1>
  <div id="back-btn" style="display:none"><button>← Back to Boards</button></div>
</header>

<main>
  <div id="page-boards">
    <!-- Existing board creation form -->
  </div>
  
  <div id="page-board-detail" style="display:none">
    <h2 id="board-title">Board Name</h2>
    <div style="display:flex;gap:8px;margin-bottom:20px">
      <input id="list-name" placeholder="Add list (e.g. To Do)" />
      <button id="add-list">Add List</button>
    </div>
    <div class="lists" id="lists"></div>
  </div>
</main>
```

### CSS Update
```css
/* Navigation styling */
#back-btn button {
    background: transparent;
    border: 1px solid rgba(255, 255, 255, 0.5);
}

#back-btn button:hover {
    background: rgba(255, 255, 255, 0.1);
}

/* Board detail page */
.lists {
    display: flex;
    gap: 12px;
    align-items: flex-start;
    overflow: auto;
}
```

### JavaScript Update
```javascript
// Feature 3: Navigation between views
const pageBoards = document.getElementById('page-boards');
const pageDetail = document.getElementById('page-board-detail');
const backBtn = document.getElementById('back-btn');
const listsEl = document.getElementById('lists');
const boardTitle = document.getElementById('board-title');
let currentBoardId = null;

// Open a specific board
function openBoard(id) {
    currentBoardId = id;
    const board = boards.find(b => b.id === id);
    
    if (!board) return;
    
    // Update UI
    boardTitle.textContent = board.name;
    pageBoards.style.display = 'none';
    pageDetail.style.display = 'block';
    backBtn.style.display = 'block';
}

// Go back to boards list
backBtn.onclick = () => {
    currentBoardId = null;
    pageBoards.style.display = 'block';
    pageDetail.style.display = 'none';
    backBtn.style.display = 'none';
};

// Update board item click to navigate
function renderBoards() {
    boardListEl.innerHTML = '';
    
    boards.forEach(board => {
        const boardDiv = document.createElement('div');
        boardDiv.className = 'board-item';
        boardDiv.textContent = board.name;
        boardDiv.onclick = () => openBoard(board.id);
        boardListEl.appendChild(boardDiv);
    });
}
```

**✅ FEATURE 3 COMPLETE**: Can navigate between boards list and board details.

---

## **Feature 4: Create Lists**

### CSS Update
```css
/* List styling */
.list {
    background: #fff;
    padding: 10px;
    border-radius: 8px;
    width: 260px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.list h3 {
    margin: 0 0 8px;
    font-size: 16px;
    cursor: text;
    padding: 4px;
}

.list h3:focus {
    background: #f0f4ff;
    outline: none;
    border-radius: 4px;
}
```

### JavaScript Update
```javascript
// Feature 4: List Management
const addListBtn = document.getElementById('add-list');
const listNameEl = document.getElementById('list-name');

// Render lists for current board
function renderLists() {
    const board = boards.find(b => b.id === currentBoardId);
    if (!board) return;
    
    listsEl.innerHTML = '';
    
    // Ensure board has lists array
    board.lists = board.lists || [];
    
    board.lists.forEach(list => {
        // Create list container
        const listDiv = document.createElement('div');
        listDiv.className = 'list';
        
        // Create list title (editable)
        const title = document.createElement('h3');
        title.textContent = list.name;
        title.contentEditable = true;
        
        listDiv.appendChild(title);
        listsEl.appendChild(listDiv);
    });
}

// Add new list
addListBtn.onclick = () => {
    const name = listNameEl.value.trim();
    if (!name) {
        alert('Please enter a list name');
        return;
    }
    
    const board = boards.find(b => b.id === currentBoardId);
    if (!board) return;
    
    // Initialize lists array if needed
    board.lists = board.lists || [];
    
    // Add new list
    board.lists.push({
        id: uid(),
        name: name,
        cards: []
    });
    
    listNameEl.value = '';
    renderLists();
};

// Update openBoard to render lists
function openBoard(id) {
    currentBoardId = id;
    const board = boards.find(b => b.id === id);
    
    if (!board) return;
    
    boardTitle.textContent = board.name;
    pageBoards.style.display = 'none';
    pageDetail.style.display = 'block';
    backBtn.style.display = 'block';
    
    renderLists();
}
```

**✅ FEATURE 4 COMPLETE**: Can create and display lists within a board.

---

## **BREAK (5 minutes)**

---

## **Feature 5: Create Cards in Lists**

### CSS Update
```css
/* Card styling */
.cards {
    display: flex;
    flex-direction: column;
    gap: 8px;
    min-height: 30px;
    margin-bottom: 10px;
}

.card {
    background: #f8fafc;
    padding: 8px;
    border-radius: 6px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
    cursor: pointer;
}

/* Add card area */
.add-area {
    margin-top: 10px;
    display: flex;
    gap: 6px;
}

.add-area input {
    flex: 1;
}
```

### JavaScript Update
```javascript
// Feature 5: Card Management
function renderLists() {
    const board = boards.find(b => b.id === currentBoardId);
    if (!board) return;
    
    listsEl.innerHTML = '';
    
    board.lists = board.lists || [];
    
    board.lists.forEach(list => {
        const listDiv = document.createElement('div');
        listDiv.className = 'list';
        
        // List title
        const title = document.createElement('h3');
        title.textContent = list.name;
        title.contentEditable = true;
        listDiv.appendChild(title);
        
        // Cards container
        const cardsDiv = document.createElement('div');
        cardsDiv.className = 'cards';
        
        // Add existing cards
        list.cards.forEach(card => {
            const cardDiv = document.createElement('div');
            cardDiv.className = 'card';
            cardDiv.textContent = card.text;
            cardsDiv.appendChild(cardDiv);
        });
        
        listDiv.appendChild(cardsDiv);
        
        // Add card input area
        const addArea = document.createElement('div');
        addArea.className = 'add-area';
        
        const input = document.createElement('input');
        input.placeholder = 'Add card';
        
        const addButton = document.createElement('button');
        addButton.textContent = 'Add';
        
        addButton.onclick = () => {
            const cardText = input.value.trim();
            if (cardText) {
                // Add card to this list
                list.cards.push({
                    id: uid(),
                    text: cardText
                });
                
                input.value = '';
                renderLists();
            }
        };
        
        // Also allow Enter key
        input.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                addButton.click();
            }
        });
        
        addArea.appendChild(input);
        addArea.appendChild(addButton);
        listDiv.appendChild(addArea);
        
        listsEl.appendChild(listDiv);
    });
}
```

**✅ FEATURE 5 COMPLETE**: Can add cards to lists.

---

## **Feature 6: Data Persistence with localStorage**

### JavaScript Update
```javascript
// Feature 6: Data Persistence
const STORAGE_KEY = 'simple_trello_boards_v1';

// Load boards from localStorage on startup
let boards = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');

// Save function
function save() {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(boards));
    console.log('Data saved to localStorage');
}

// Update all functions that modify data to call save()

// Update addBoardBtn.onclick
addBoardBtn.onclick = () => {
    const name = boardNameEl.value.trim();
    if (!name) {
        alert('Please enter a board name');
        return;
    }
    
    boards.push({
        id: uid(),
        name: name,
        lists: []
    });
    
    save(); // Save after adding
    boardNameEl.value = '';
    renderBoards();
};

// Update addListBtn.onclick
addListBtn.onclick = () => {
    const name = listNameEl.value.trim();
    if (!name) {
        alert('Please enter a list name');
        return;
    }
    
    const board = boards.find(b => b.id === currentBoardId);
    if (!board) return;
    
    board.lists = board.lists || [];
    board.lists.push({
        id: uid(),
        name: name,
        cards: []
    });
    
    save(); // Save after adding
    listNameEl.value = '';
    renderLists();
};

// Update card addition
addButton.onclick = () => {
    const cardText = input.value.trim();
    if (cardText) {
        list.cards.push({
            id: uid(),
            text: cardText
        });
        
        save(); // Save after adding
        input.value = '';
        renderLists();
    }
};

// Update list title editing
title.contentEditable = true;
title.onblur = () => {
    list.name = title.textContent.trim();
    save(); // Save after editing
};

// Initialize on page load
document.addEventListener('DOMContentLoaded', () => {
    renderBoards();
});
```

**✅ FEATURE 6 COMPLETE**: Data persists across page reloads.

---

## **BREAK (5 minutes)**

---

## **Feature 7: Edit Card Names**

### JavaScript Update
```javascript
// Feature 7: Edit Card Names
function renderLists() {
    const board = boards.find(b => b.id === currentBoardId);
    if (!board) return;
    
    listsEl.innerHTML = '';
    
    board.lists = board.lists || [];
    
    board.lists.forEach(list => {
        // ... existing list creation code ...
        
        // Add existing cards
        list.cards.forEach(card => {
            const cardDiv = document.createElement('div');
            cardDiv.className = 'card';
            cardDiv.textContent = card.text;
            
            // Double-click to edit card
            cardDiv.ondblclick = () => {
                const newText = prompt('Edit card name:', card.text);
                if (newText !== null && newText.trim() !== '') {
                    card.text = newText.trim();
                    save();
                    renderLists();
                }
            };
            
            cardsDiv.appendChild(cardDiv);
        });
        
        // ... rest of the function ...
    });
}
```

**✅ FEATURE 7 COMPLETE**: Can edit card names by double-clicking.

---

## **Feature 8: Drag and Drop Cards**

### CSS Update
```css
/* Drag and drop styling */
.card {
    /* existing styles */
    cursor: grab;
}

.card:active {
    cursor: grabbing;
}

.cards {
    /* Add min-height for empty lists */
    min-height: 40px;
    transition: background-color 0.2s;
}

.cards.drag-over {
    background-color: #e0e7ff;
    border-radius: 4px;
}
```

### JavaScript Update
```javascript
// Feature 8: Drag and Drop
function renderLists() {
    const board = boards.find(b => b.id === currentBoardId);
    if (!board) return;
    
    listsEl.innerHTML = '';
    
    board.lists = board.lists || [];
    
    board.lists.forEach(list => {
        // ... existing list creation code ...
        
        // Make cards container droppable
        cardsDiv.addEventListener('dragover', (e) => {
            e.preventDefault();
            cardsDiv.classList.add('drag-over');
        });
        
        cardsDiv.addEventListener('dragleave', () => {
            cardsDiv.classList.remove('drag-over');
        });
        
        cardsDiv.addEventListener('drop', (e) => {
            e.preventDefault();
            cardsDiv.classList.remove('drag-over');
            
            const cardId = e.dataTransfer.getData('card-id');
            const sourceListId = e.dataTransfer.getData('source-list');
            
            // Find the source list
            const sourceList = board.lists.find(l => l.id === sourceListId);
            if (!sourceList) return;
            
            // Find the card
            const cardIndex = sourceList.cards.findIndex(c => c.id === cardId);
            if (cardIndex === -1) return;
            
            // Remove from source list
            const [card] = sourceList.cards.splice(cardIndex, 1);
            
            // Add to current list
            list.cards.push(card);
            
            save();
            renderLists();
        });
        
        // Add existing cards
        list.cards.forEach(card => {
            const cardDiv = document.createElement('div');
            cardDiv.className = 'card';
            cardDiv.textContent = card.text;
            cardDiv.draggable = true;
            
            // Double-click to edit
            cardDiv.ondblclick = () => {
                const newText = prompt('Edit card name:', card.text);
                if (newText !== null && newText.trim() !== '') {
                    card.text = newText.trim();
                    save();
                    renderLists();
                }
            };
            
            // Drag start
            cardDiv.addEventListener('dragstart', (e) => {
                e.dataTransfer.setData('card-id', card.id);
                e.dataTransfer.setData('source-list', list.id);
                cardDiv.style.opacity = '0.4';
            });
            
            cardDiv.addEventListener('dragend', () => {
                cardDiv.style.opacity = '1';
            });
            
            cardsDiv.appendChild(cardDiv);
        });
        
        // ... rest of the function ...
    });
}
```

**✅ FEATURE 8 COMPLETE**: Can drag and drop cards between lists.

---

## **Feature 9: Polish and Final Touches**

### CSS Updates for Polish
```css
/* Add smooth transitions */
.board-item, .list, .card {
    transition: all 0.2s ease;
}

/* Improve input focus states */
input:focus {
    border-color: #2563eb;
    box-shadow: 0 0 0 2px rgba(37, 99, 235, 0.1);
}

/* Make lists scrollable on small screens */
.lists {
    overflow-x: auto;
    padding-bottom: 10px;
}

/* Scrollbar styling */
.lists::-webkit-scrollbar {
    height: 8px;
}

.lists::-webkit-scrollbar-track {
    background: #f1f1f1;
    border-radius: 4px;
}

.lists::-webkit-scrollbar-thumb {
    background: #c1c1c1;
    border-radius: 4px;
}

.lists::-webkit-scrollbar-thumb:hover {
    background: #a1a1a1;
}
```

### JavaScript Final Polish
```javascript
// Add Enter key support for inputs

// For board name input
boardNameEl.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addBoardBtn.click();
    }
});

// For list name input
listNameEl.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addListBtn.click();
    }
});

// Prevent empty list/card names
title.contentEditable = true;
title.onblur = () => {
    const newName = title.textContent.trim();
    if (newName) {
        list.name = newName;
        save();
    } else {
        title.textContent = list.name; // Revert if empty
    }
};

// Add card edit with better UX
cardDiv.ondblclick = () => {
    const newText = prompt('Edit card name:', card.text);
    if (newText !== null) {
        const trimmed = newText.trim();
        if (trimmed) {
            card.text = trimmed;
            save();
            renderLists();
        }
    }
};
```

### HTML Final Touches
```html
<!-- Add placeholder instructions -->
<div style="color: #666; font-size: 14px; margin-top: 20px;">
  <p><strong>How to use:</strong></p>
  <ul>
    <li>Create boards for different projects</li>
    <li>Add lists (e.g., To Do, Doing, Done)</li>
    <li>Add cards for tasks</li>
    <li>Drag cards between lists</li>
    <li>Double-click cards to edit</li>
  </ul>
</div>
```

**✅ FEATURE 9 COMPLETE**: Polished application with good UX.

---

## **🎉 PROJECT COMPLETE!**

### **Testing Checklist**
Test each feature in order:
1. ✅ Create a new board
2. ✅ Click board to open details
3. ✅ Go back to boards list
4. ✅ Create lists within a board
5. ✅ Add cards to lists
6. ✅ Refresh page - data should persist
7. ✅ Double-click card to edit name
8. ✅ Drag card to another list
9. ✅ Edit list name by clicking on it

### **Common Issues & Solutions**
1. **Data not saving**: Check DevTools → Application → LocalStorage
2. **Drag not working**: Ensure `e.preventDefault()` in dragover
3. **Blank page on refresh**: Check JSON parsing in localStorage
4. **Cards disappear when dragging**: Check card ID matching

### **Extension Ideas for Advanced Learners**
1. Add card deletion (right-click or trash icon)
2. Add list deletion
3. Add board colors/themes
4. Add due dates to cards
5. Add card descriptions
6. Add search functionality
7. Make responsive for mobile

