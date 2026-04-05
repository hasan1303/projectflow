# ProjectFlow – SaaS Project Management Dashboard

A modern Kanban-style project management app built with **Vue 3** and **Vite**, designed as a portfolio-ready SaaS dashboard.

ProjectFlow helps users create projects, manage tasks, track progress, organize workflow visually, and stay productive through a clean and responsive interface.

---

## Preview

![ProjectFlow Preview](./preview.png)

> The repository includes a `preview.png` image in the root folder.

---

## Features

### Core Features
- Create and manage multiple projects
- Add, edit, and delete tasks
- Organize tasks into Kanban columns:
  - To Do
  - In Progress
  - Done
- Drag and drop tasks between columns
- Search tasks by title, description, or labels
- Filter tasks by priority
- Sort tasks by:
  - Newest first
  - Oldest first
  - Due date: soonest
  - Due date: latest
- Track progress with dashboard stats
- View overdue task styling
- Task labels/tags support
- Delete confirmation modals
- Project summary sidebar
- Activity log for project actions
- Toast notifications for user feedback
- Dark mode
- Data persistence with localStorage
- Responsive SaaS-style UI

---

## Built With

- **Vue 3**
- **Vite**
- **JavaScript**
- **CSS3**
- **localStorage**

---

## UI Highlights

- Clean dashboard layout
- Premium sidebar navigation
- Soft shadows and rounded cards
- Strong typography and spacing
- Modern Kanban board structure
- Mobile responsive experience
- Light / dark theme support

---

## Project Structure

```text
src/
├── assets/
│   └── styles.css
├── components/
│   ├── AddTaskModal.vue
│   ├── AppHeader.vue
│   ├── EditTaskModal.vue
│   ├── PriorityFilter.vue
│   ├── ProjectBoard.vue
│   ├── ProjectSelector.vue
│   ├── SearchBar.vue
│   ├── SortSelect.vue
│   ├── TaskCard.vue
│   ├── TaskColumn.vue
│   └── ToastContainer.vue
├── composables/
│   └── useLocalStorage.js
├── App.vue
└── main.js
```

---

## Getting Started

### Clone the repository

```bash
git clone https://github.com/your-username/projectflow.git
```

### Go into the project folder

```bash
cd projectflow
```

### Install dependencies

```bash
npm install
```

### Run development server

```bash
npm run dev
```

---


## Deploy to GitHub Pages

This project is already prepared for GitHub Pages deployment with **GitHub Actions**.

### What is included
- `vite.config.js` configured with `base: './'` so assets work on GitHub Pages
- `.github/workflows/deploy.yml` for automatic deployment on every push to `main`

### Steps
1. Create a GitHub repository and push this project to the `main` branch.
2. In your repository, open **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to **GitHub Actions**.
4. Push again to `main` if needed, or run the workflow manually from the **Actions** tab.
5. After deployment finishes, your app will be available on your GitHub Pages URL.

### Notes
- If your repo is named `<username>.github.io`, the site will publish at the root domain.
- For a normal repository, GitHub Pages will publish it under a repo subpath. Using `base: './'` keeps the built asset paths portable for this setup.

## Build for Production

```bash
npm run build
```

---

## Why I Built This Project

I built this project to strengthen my frontend development skills with Vue and to create a more realistic portfolio piece that reflects how modern SaaS dashboards are structured.

This project demonstrates:

- component-based architecture
- state handling in Vue
- CRUD operations
- drag and drop interaction
- filtering and sorting logic
- localStorage persistence
- responsive UI design
- product-oriented thinking

---

## Future Improvements

- Team members and avatars
- Authentication
- Backend integration with Firebase or Supabase
- Comments on tasks
- Analytics dashboard
- Task attachments
- Real-time collaboration
- Role-based access

---

## Author

**Hasan Pisli**

- Computer Science
- Frontend / WordPress Developer
- Vue.js, HTML, CSS, JavaScript

---

## License

This project is for portfolio and learning purposes.
