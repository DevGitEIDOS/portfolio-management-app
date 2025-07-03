# Supabase Setup Guide for Portfolio Management Application

## 🚀 Quick Setup

### 1. Create Supabase Project
1. Go to [supabase.com](https://supabase.com)
2. Sign up/Login and create a new project
3. Wait for the project to be ready (usually 1-2 minutes)

### 2. Get Your Credentials
1. Go to **Settings** → **API** in your Supabase dashboard
2. Copy your **Project URL** and **anon public** key
3. Replace the placeholders in the HTML file:
   ```javascript
   const SUPABASE_URL = 'YOUR_SUPABASE_URL'; // Replace with your URL
   const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY'; // Replace with your key
   ```

### 3. Create Database Tables

Run these SQL commands in your Supabase **SQL Editor**:

#### Users Table
```sql
CREATE TABLE users (
    id TEXT PRIMARY KEY,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    password TEXT NOT NULL,
    role TEXT NOT NULL CHECK (role IN ('admin', 'manager', 'member')),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Create policy to allow all operations (for demo purposes)
CREATE POLICY "Allow all operations on users" ON users
    FOR ALL USING (true);
```

#### Projects Table
```sql
CREATE TABLE projects (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    client TEXT NOT NULL,
    location TEXT NOT NULL,
    project_manager TEXT NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    team_members TEXT[] NOT NULL,
    total_efforts INTEGER DEFAULT 0,
    status TEXT NOT NULL,
    rag_status TEXT NOT NULL CHECK (rag_status IN ('green', 'yellow', 'red')),
    status_comments TEXT,
    last_updated DATE NOT NULL,
    created_by TEXT NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;

-- Create policy to allow all operations (for demo purposes)
CREATE POLICY "Allow all operations on projects" ON projects
    FOR ALL USING (true);
```

#### Tasks Table
```sql
CREATE TABLE tasks (
    id TEXT PRIMARY KEY,
    project_id TEXT NOT NULL REFERENCES projects(id) ON DELETE CASCADE,
    name TEXT NOT NULL,
    owner TEXT NOT NULL,
    due_date DATE NOT NULL,
    status TEXT NOT NULL CHECK (status IN ('not-started', 'in-progress', 'on-hold', 'completed')),
    created_date DATE NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE tasks ENABLE ROW LEVEL SECURITY;

-- Create policy to allow all operations (for demo purposes)
CREATE POLICY "Allow all operations on tasks" ON tasks
    FOR ALL USING (true);
```

### 4. Update Your HTML File

Replace the Supabase configuration in your HTML file:

```javascript
// Replace these values with your actual Supabase credentials
const SUPABASE_URL = 'https://your-project-id.supabase.co';
const SUPABASE_ANON_KEY = 'your-anon-key-here';
```

## 🔧 Features

### ✅ What's Working
- **User Authentication**: Login/logout with email/password
- **User Management**: Add, edit, delete users with roles
- **Project Management**: Full CRUD operations for projects
- **Task Management**: Add, edit, delete tasks within projects
- **Role-Based Access**: Different permissions for admin/manager/member
- **Data Persistence**: All data stored in Supabase database
- **Offline Fallback**: localStorage backup if database is unavailable
- **Real-time Sync**: Changes are immediately saved to database

### 🔒 Security Features
- **Row Level Security**: Database-level access control
- **Input Validation**: Client-side and server-side validation
- **Password Protection**: Secure password storage (use hashing in production)
- **Session Management**: Secure login/logout with localStorage

### 📊 Database Schema

#### Users Table
| Column | Type | Description |
|--------|------|-------------|
| id | TEXT | Unique user identifier |
| first_name | TEXT | User's first name |
| last_name | TEXT | User's last name |
| email | TEXT | User's email (unique) |
| password | TEXT | User's password |
| role | TEXT | User role (admin/manager/member) |
| created_at | TIMESTAMP | Account creation date |
| updated_at | TIMESTAMP | Last update date |

#### Projects Table
| Column | Type | Description |
|--------|------|-------------|
| id | TEXT | Unique project identifier |
| name | TEXT | Project name |
| client | TEXT | Client name |
| location | TEXT | Project location |
| project_manager | TEXT | Project manager name |
| start_date | DATE | Project start date |
| end_date | DATE | Project end date |
| team_members | TEXT[] | Array of team member names |
| total_efforts | INTEGER | Total hours allocated |
| status | TEXT | Project status |
| rag_status | TEXT | RAG status (green/yellow/red) |
| status_comments | TEXT | Latest status update |
| last_updated | DATE | Last update date |
| created_by | TEXT | User who created the project |
| created_at | TIMESTAMP | Project creation date |
| updated_at | TIMESTAMP | Last update date |

#### Tasks Table
| Column | Type | Description |
|--------|------|-------------|
| id | TEXT | Unique task identifier |
| project_id | TEXT | Reference to parent project |
| name | TEXT | Task name |
| owner | TEXT | Task owner name |
| due_date | DATE | Task due date |
| status | TEXT | Task status |
| created_date | DATE | Task creation date |
| created_at | TIMESTAMP | Record creation date |
| updated_at | TIMESTAMP | Last update date |

## 🚨 Important Notes

### Security Considerations
1. **Password Hashing**: In production, implement proper password hashing (bcrypt, argon2)
2. **JWT Tokens**: Consider implementing JWT for better session management
3. **API Keys**: Never expose your service role key in client-side code
4. **Row Level Security**: Implement proper RLS policies for production

### Performance Optimization
1. **Indexing**: Add indexes on frequently queried columns
2. **Pagination**: Implement pagination for large datasets
3. **Caching**: Consider implementing client-side caching
4. **Real-time**: Enable real-time subscriptions for live updates

### Backup Strategy
1. **Database Backups**: Supabase provides automatic backups
2. **Export Data**: Use the export feature in the app
3. **Version Control**: Keep your database schema in version control

## 🔄 Migration from localStorage

If you have existing data in localStorage:

1. **Export Current Data**: Use the export feature in your app
2. **Import to Supabase**: The app will automatically sync with Supabase
3. **Verify Data**: Check that all data is properly migrated

## 🆘 Troubleshooting

### Common Issues

1. **CORS Errors**: Make sure your Supabase URL is correct
2. **Authentication Errors**: Check your API keys
3. **Data Not Loading**: Check browser console for errors
4. **Permission Errors**: Verify RLS policies are set correctly

### Debug Mode
Enable debug logging by adding this to your browser console:
```javascript
localStorage.setItem('debug', 'true');
```

## 📞 Support

For issues with:
- **Supabase**: Check [Supabase Documentation](https://supabase.com/docs)
- **Application**: Check browser console for error messages
- **Database**: Use Supabase dashboard to inspect tables and data 