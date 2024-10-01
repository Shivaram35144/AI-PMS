# AI-PMS

## 1. PAGES FINALISED

1. Auth
   - Login (Route: /login) 
   - Signup (Route: /signup)
   - Forgot Password (Route: /forgot-password)

2. Dashboard (Role-Based Routes)
   - Admin dashboard (Route: /admin/dashboard)
     - Displays project overview, team performance, user management.
   - Project Manager dashboard (Route: /pm/dashboard)
     - Displays project progress, task distribution, and AI insights about bottlenecks or priorities.
   - User dashboard (Route: /user/dashboard)
     - Shows individual tasks, upcoming deadlines, and status updates.

3. Projects
   - Projects Overview (Route: /projects)
     - Displays all active projects, progress, and deadlines.
   - Create Project (Route: /projects/create)
     - Simple form for creating a new project.
   - Project Details (Route: /projects/:projectId) //dynamic api
     - Displays project details, tasks, and team members.
   - Kanban Task Board (Route: /projects/:projectId/tasks)
     - Drag-and-drop Kanban board with task statuses (To Do, In Progress, Done).
   - Create Task (Route: /projects/:projectId/tasks/create)
     - Form for creating new tasks with AI-powered suggestions.
   - Task Details (Route: /projects/:projectId/tasks/:taskId)
     - Detailed task view including **comments**, history, and files.


5. AI Insights
   - AI Insights Overview (Route: /projects/:projectId/insights)
     - AI-generated insights like bottlenecks, predicted delays, and task prioritization.
   - Task Assignment Suggestions (Integrated in Task Creation)
     - AI suggests task assignments based on team members' skills and workload.
   - Priority Recommendations (Integrated in Task Creation)
     - AI suggests task priorities based on deadlines and project needs.

6. Reporting
   - Project Reports (Route: /reports/projects)
     - Automated reports showing project status and task completion rates.
   - Team Performance (Route: /reports/team-performance)
     - Insights into team member performance, tasks, and deadlines.

7. User Management
   - User Management (Route: /admin/users)
     - Admin can add, remove, or modify user roles and update team member information.
   - User Profile (Route: /users/:userId/profile)
     - Displays user profile information, including tasks and skills.

8. Settings and Onboarding
   - Settings (Route: /settings)
     - Users can update profiles, change passwords, and manage notification preferences.
   - Onboarding/Tutorial (Route: /onboarding)
     - A guided tour to help new users understand the platform.

9. Resource Sharing
   - Resource Library (Route: /resources)
     - A shared space where users can upload and share resources, categorized by AI.

Protected Routes
   - Authenticated Users
     - /projects, /tasks, /user/dashboard
   - Admin
     - /admin/dashboard, /admin/users, /admin/settings
   - Project Manager
     - /pm/dashboard, /projects/create, /projects/:projectId/insights, /reports/projects
   - File Sharing
     - /projects/:projectId/tasks/:taskId/files
   - AI Insights
     - /projects/:projectId/insights (Restricted to Admins and Project Managers)




HAVE TO DO THE WIREFRAMES AND DESIGN

## 2. BACKEND API ROUTES

1. **Get AI Insights for a Project**
   - **Method**: GET
   - **Route**: `/api/projects/:projectId/insights`
   - **Description**: Fetch AI-generated insights for a specific project, including bottlenecks, predicted delays, and task prioritization.
   - **Access**: Project Manager, Admin

2. **Get Task Assignment Suggestions**
   - **Method**: GET
   - **Route**: `/api/projects/:projectId/tasks/assignment-suggestions`
   - **Description**: Provides AI-powered suggestions for task assignment based on team members’ skills and current workload.
   - **Access**: Project Manager, Admin

3. **Get Task Priority Recommendations**
   - **Method**: GET
   - **Route**: `/api/projects/:projectId/tasks/priority-recommendations`
   - **Description**: Suggests task priority based on deadlines, task complexity, and overall project needs.
   - **Access**: Project Manager, Admin

4. **Generate AI Report on Bottlenecks**
   - **Method**: GET
   - **Route**: `/api/projects/:projectId/ai/bottlenecks`
   - **Description**: Generates a report on potential bottlenecks identified by AI algorithms.
   - **Access**: Project Manager, Admin

5. **Generate AI Predictions for Delays**
   - **Method**: GET
   - **Route**: `/api/projects/:projectId/ai/predictions/delays`
   - **Description**: Predicts project or task delays based on historical data and current progress.
   - **Access**: Project Manager, Admin

6. **AI Task Automation Triggers**
   - **Method**: POST
   - **Route**: `/api/projects/:projectId/tasks/:taskId/ai-trigger`
   - **Description**: Triggers AI-based automation for task management, like task reassignment or status update recommendations.
   - **Access**: Project Manager, Admin

7. **Fetch Overall Project Insights**
   - **Method**: GET
   - **Route**: `/api/projects/:projectId/ai/overall-insights`
   - **Description**: Fetches a summary of AI-powered insights, including project health, potential risks, and recommendations.
   - **Access**: Project Manager, Admin


## 3. FINALIZED DB SCHEMA
1. USER:


| Column Name | Data Type | Constraints |
|---|---|---|
| user_id | SERIAL | PRIMARY KEY |
| name | VARCHAR(100) | NOT NULL |
| email | VARCHAR(100) | UNIQUE, NOT NULL |
| password_hash | VARCHAR(255) | NOT NULL |
| role | VARCHAR(50) | CHECK(role IN ('Admin', 'Project Manager', 'User')), DEFAULT 'User' |
| skills | TEXT[] | Array of skills |
| availability | BOOLEAN | DEFAULT TRUE |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

2. PROJECT

| Column Name | Data Type | Constraints |
|---|---|---|
| project_id | SERIAL | PRIMARY KEY |
| project_name | VARCHAR(150) | NOT NULL |
| description | TEXT |  |
| status | VARCHAR(50) | CHECK(status IN ('Active', 'Completed', 'On Hold')), DEFAULT 'Active' |
| deadline | TIMESTAMP |  |
| created_by | INT | FOREIGN KEY REFERENCES users(user_id) ON DELETE CASCADE |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |


3. TASKS

| Column Name | Data Type | Constraints |
|---|---|---|
| task_id | SERIAL | PRIMARY KEY |
| project_id | INT | FOREIGN KEY REFERENCES projects(project_id) ON DELETE CASCADE |
| task_name | VARCHAR(150) | NOT NULL |
| description | TEXT |  |
| status | VARCHAR(50) | CHECK(status IN ('To Do', 'In Progress', 'Done')), DEFAULT 'To Do' |
| priority | VARCHAR(50) | CHECK(priority IN ('Low', 'Medium', 'High')), DEFAULT 'Medium' |
| assigned_to | INT | FOREIGN KEY REFERENCES users(user_id) ON DELETE SET NULL |
| due_date | TIMESTAMP |  |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |
| updated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |


4. COMMENTS

| Column Name | Data Type | Constraints |
|---|---|---|
| comment_id | SERIAL | PRIMARY KEY |
| task_id | INT | FOREIGN KEY REFERENCES tasks(task_id) ON DELETE CASCADE |
| user_id | INT | FOREIGN KEY REFERENCES users(user_id) ON DELETE CASCADE |
| comment_text | TEXT | NOT NULL |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

5. AI INSIGHTS

| Column Name | Data Type | Constraints |
|---|---|---|
| insight_id | SERIAL | PRIMARY KEY |
| project_id | INT | FOREIGN KEY REFERENCES projects(project_id) ON DELETE CASCADE |
| insight_text | TEXT | NOT NULL |
| generated_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP |

RELATIONS:

* Users-Projects: One-to-Many
* Projects-Tasks: One-to-Many
* Users-Tasks: One-to-Many -- A user can be assigned to many tasks, and each task can have one user assigned
* Tasks-Comments: One-to-Many
* Projects-AI Insights: One-to-Many