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

## 4. Step-by-Step Development

### **Step 1: HTML Structure (15 mins)**
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
    <div id="back-btn" style="display:none"><button>← Back to Boards</button></div>
  </header>
  <main>
    <!-- Board List View -->
    <div id="page-boards">
      <h2>Create Board</h2>
      <div style="display:flex;gap:8px;margin-bottom:20px">
        <input id="board-name" placeholder="Board name" />
        <button id="add-board">Add</button>
      </div>
      <div class="board-list" id="board-list"></div>
    </div>

    <!-- Board Detail View -->
    <div id="page-board-detail" style="display:none">
      <h2 id="board-title"></h2>
      <div style="display:flex;gap:8px;margin-bottom:20px">
        <input id="list-name" placeholder="Add list (e.g. To Do)" />
        <button id="add-list">Add List</button>
      </div>
      <div class="lists" id="lists"></div>
    </div>
  </main>

  <script src="app.js"></script>
</body>
</html>
```

**Explanation**: We create two main views (pages) that we'll toggle between. One shows all boards, the other shows a specific board's details.

---

### **Step 2: Basic CSS Styling (15 mins)**
```css
body {
    font-family: Inter, Arial, sans-serif;
    background: #f5f6fa;
    margin: 0;
    padding: 0
}

header {
    background: #2563eb;
    color: #fff;
    padding: 10px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center
}

/* ... (rest of the CSS from styles.css) */
```

**Key Concepts**:
- Use flexbox for layouts
- Create visual hierarchy with colors and shadows
- Make elements interactive with hover states
- Ensure proper spacing and alignment

---

### **Step 3: JavaScript - Board Management (20 mins)**
```javascript
const STORAGE_KEY = 'simple_trello_boards_v2';
let boards = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
let currentBoardId = null;

function uid() { 
    return Date.now().toString(36) + Math.random().toString(36).slice(2, 7) 
}

function renderBoards() {
    boardListEl.innerHTML = '';
    boards.forEach(b => {
        const div = document.createElement('div');
        div.className = 'board-item';
        div.textContent = b.name;
        div.onclick = () => openBoard(b.id);
        boardListEl.appendChild(div);
    });
}

addBoardBtn.onclick = () => {
    const name = boardNameEl.value.trim();
    if (!name) return;
    boards.push({ id: uid(), name, lists: [] });
    save();
    boardNameEl.value = '';
    renderBoards();
};
```

**What we're doing**:
1. Initialize data from localStorage
2. Create unique IDs for each board
3. Render boards dynamically
4. Add new boards with click handler
5. Save to localStorage after each change

---

### **BREAK (5 mins)**

---

### **Step 4: Navigation Between Views (15 mins)**
```javascript
function openBoard(id) {
    currentBoardId = id;
    const board = boards.find(b => b.id === id);
    boardTitle.textContent = board.name;
    pageBoards.style.display = 'none';
    pageDetail.style.display = 'block';
    backBtn.style.display = 'block';
    renderLists();
}

backBtn.onclick = () => {
    currentBoardId = null;
    pageBoards.style.display = 'block';
    pageDetail.style.display = 'none';
    backBtn.style.display = 'none';
    renderBoards();
};
```

**Explanation**: We toggle visibility of two main divs to simulate page navigation. We track the current board ID to know which board to display.

---

### **Step 5: List and Card Rendering (20 mins)**
```javascript
function renderLists() {
    const board = boards.find(b => b.id === currentBoardId);
    listsEl.innerHTML = '';
    board.lists?.forEach(list => {
        // Create list container
        const listDiv = document.createElement('div');
        listDiv.className = 'list';
        
        // Add editable title
        const title = document.createElement('h3');
        title.textContent = list.name;
        title.contentEditable = true;
        title.onblur = () => { 
            list.name = title.textContent.trim(); 
            save() 
        };
        
        // Create cards container
        const cardsDiv = document.createElement('div');
        cardsDiv.className = 'cards';
        
        // Add cards to the list
        list.cards.forEach(card => {
            const c = document.createElement('div');
            c.className = 'card';
            c.textContent = card.text;
            c.ondblclick = () => {
                const newName = prompt('Edit card name', card.text);
                if (newName !== null) { 
                    card.text = newName.trim(); 
                    save(); 
                    renderLists(); 
                }
            };
            cardsDiv.appendChild(c);
        });
        
        // Add card creation area
        const addArea = document.createElement('div');
        addArea.className = 'add-area';
        const input = document.createElement('input'); 
        input.placeholder = 'Add card';
        const btn = document.createElement('button'); 
        btn.textContent = 'Add';
        btn.onclick = () => { 
            if (input.value.trim()) { 
                list.cards.push({ 
                    id: uid(), 
                    text: input.value.trim() 
                }); 
                input.value = ''; 
                save(); 
                renderLists(); 
            } 
        };
        
        // Assemble everything
        listDiv.appendChild(title);
        listDiv.appendChild(cardsDiv);
        listDiv.appendChild(addArea);
        listsEl.appendChild(listDiv);
    });
}
```

**Key Features**:
- Dynamic DOM creation
- Editable list titles
- Double-click to edit cards
- Add new cards within each list

---

### **BREAK (5 mins)**

---

### **Step 6: Drag and Drop Implementation (20 mins)**
```javascript
// Make cards draggable
c.draggable = true;
c.ondragstart = e => { 
    e.dataTransfer.setData('card', card.id); 
    e.dataTransfer.setData('list', list.id) 
};

// Make list containers droppable
cardsDiv.addEventListener('dragover', e => { 
    e.preventDefault() 
});

cardsDiv.addEventListener('drop', e => {
    e.preventDefault();
    const cardId = e.dataTransfer.getData('card');
    const fromListId = e.dataTransfer.getData('list');
    
    if (fromListId !== list.id) {
        const fromList = board.lists.find(l => l.id === fromListId);
        const card = fromList.cards.find(c => c.id === cardId);
        
        // Remove from original list
        fromList.cards = fromList.cards.filter(c => c.id !== cardId);
        
        // Add to new list
        list.cards.push(card);
        
        save(); 
        renderLists();
    }
});
```

**Drag and Drop API Concepts**:
1. `draggable="true"` makes elements draggable
2. `ondragstart` sets data to transfer
3. `dragover` event must prevent default to allow drop
4. `drop` event handles the actual drop action
5. `dataTransfer` object moves data between elements

---

### **Step 7: Data Persistence with localStorage (10 mins)**
```javascript
const save = () => localStorage.setItem(STORAGE_KEY, JSON.stringify(boards));

// Save after every change:
// - Adding/editing/deleting boards
// - Adding/editing/deleting lists
// - Adding/editing/moving cards
```

**How localStorage works**:
- Stores data as key-value pairs (strings only)
- `JSON.stringify()` converts objects to strings
- `JSON.parse()` converts strings back to objects
- Data persists across page refreshes

---

### **Step 8: Polish and Enhancements (15 mins)**

1. **Add visual feedback for dragging**:
```css
.card.dragging {
    opacity: 0.6;
}
```

2. **Improve UX with Enter key support**:
```javascript
input.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        btn.click();
    }
});
```

3. **Add delete functionality**:
```javascript
// Add delete button to cards
const deleteBtn = document.createElement('button');
deleteBtn.textContent = '×';
deleteBtn.onclick = () => {
    list.cards = list.cards.filter(c => c.id !== card.id);
    save();
    renderLists();
};
```

---

### **Final Review & Testing (10 mins)**

**Test Checklist**:
- [ ] Create a new board
- [ ] Navigate to board detail
- [ ] Add multiple lists
- [ ] Add cards to lists
- [ ] Edit list names
- [ ] Edit card names (double-click)
- [ ] Drag cards between lists
- [ ] Refresh page (data should persist)
- [ ] Navigate back to boards list
- [ ] Create another board

**Common Issues & Solutions**:
1. **Drag and drop not working**: Ensure `e.preventDefault()` in dragover
2. **Data not saving**: Check localStorage in DevTools (Application tab)
3. **UI not updating**: Verify `renderLists()` is called after changes
4. **Cards disappearing**: Check that card IDs are unique

---

## **Next Steps & Extensions**

If you have extra time, consider adding:
1. **Card deletion** with a delete button
2. **List deletion** with confirmation
3. **Board deletion**
4. **Due dates** for cards
5. **Card descriptions**
6. **Labels/tags** for cards
7. **Search functionality**
8. **Responsive design** for mobile

---
