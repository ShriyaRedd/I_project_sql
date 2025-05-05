-- Create Users Table
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role_id INT,
    FOREIGN KEY (role_id) REFERENCES roles(role_id) ON DELETE SET NULL
);

-- Create Roles Table
CREATE TABLE roles (
    role_id INT AUTO_INCREMENT PRIMARY KEY,
    role_name VARCHAR(255) NOT NULL,
    role_description TEXT
);

-- Create Skills Table
CREATE TABLE skills (
    skill_id INT AUTO_INCREMENT PRIMARY KEY,
    skill_name VARCHAR(255) NOT NULL,
    skill_description TEXT
);

-- Create Permissions Table
CREATE TABLE permissions (
    permission_id INT AUTO_INCREMENT PRIMARY KEY,
    permission_name VARCHAR(255) NOT NULL,
    permission_description TEXT
);

-- Create Tasks Table
CREATE TABLE tasks (
    task_id INT AUTO_INCREMENT PRIMARY KEY,
    task_name VARCHAR(255) NOT NULL,
    task_description TEXT NOT NULL,
    status ENUM('Pending', 'In Progress', 'Completed', 'Overdue') DEFAULT 'Pending',
    deadline DATE,
    created_by INT,
    created_on TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (created_by) REFERENCES users(user_id) ON DELETE SET NULL
);

-- Create Task Assignments Table
CREATE TABLE task_assignments (
    task_id INT,
    user_id INT,
    assigned_on TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    assigned_by INT,
    status ENUM('Assigned', 'In Progress', 'Completed', 'Blocked') DEFAULT 'Assigned',
    PRIMARY KEY (task_id, user_id),
    FOREIGN KEY (task_id) REFERENCES tasks(task_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (assigned_by) REFERENCES users(user_id) ON DELETE SET NULL
);

-- Create Task Skills Table (Many-to-Many between Tasks and Skills)
CREATE TABLE task_skills (
    task_id INT,
    skill_id INT,
    PRIMARY KEY (task_id, skill_id),
    FOREIGN KEY (task_id) REFERENCES tasks(task_id) ON DELETE CASCADE,
    FOREIGN KEY (skill_id) REFERENCES skills(skill_id) ON DELETE CASCADE
);

-- Create Task Updates Table
CREATE TABLE task_updates (
    update_id INT AUTO_INCREMENT PRIMARY KEY,
    task_id INT,
    updated_by INT,
    update_description TEXT,
    update_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (task_id) REFERENCES tasks(task_id) ON DELETE CASCADE,
    FOREIGN KEY (updated_by) REFERENCES users(user_id) ON DELETE SET NULL
);

-- Create Role Permissions Table (Many-to-Many between Roles and Permissions)
CREATE TABLE role_permissions (
    role_id INT,
    permission_id INT,
    PRIMARY KEY (role_id, permission_id),
    FOREIGN KEY (role_id) REFERENCES roles(role_id) ON DELETE CASCADE,
    FOREIGN KEY (permission_id) REFERENCES permissions(permission_id) ON DELETE CASCADE
);

-- Create User Skills Table (Many-to-Many between Users and Skills)
CREATE TABLE user_skills (
    user_id INT,
    skill_id INT,
    proficiency_level ENUM('Beginner', 'Intermediate', 'Advanced') DEFAULT 'Beginner',
    acquired_on DATE,
    PRIMARY KEY (user_id, skill_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    FOREIGN KEY (skill_id) REFERENCES skills(skill_id) ON DELETE CASCADE
);

-- Create Notifications Table
CREATE TABLE notifications (
    notification_id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    notification_message TEXT,
    notification_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);


CREATE TABLE EmployeeSkills (
    employee_skill_id INT AUTO_INCREMENT PRIMARY KEY,   -- Unique ID for each entry
    user_id INT NOT NULL,                                -- Foreign Key referencing Users table
    skill_id INT NOT NULL,                               -- Foreign Key referencing Skills table
    proficiency_level VARCHAR(50) NOT NULL,              -- Skill proficiency level (e.g., Beginner, Intermediate, Expert)
    certification_status VARCHAR(50),                    -- Status of certification (Certified, Pending, None)
    years_of_experience INT,                             -- Number of years of experience in the skill
    last_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP,    -- Timestamp for when the skill data was last updated
    FOREIGN KEY (user_id) REFERENCES Users(user_id),    -- Assuming Users table exists
    FOREIGN KEY (skill_id) REFERENCES Skills(skill_id)  -- Assuming Skills table exists
);


CREATE TABLE TaskProgress (
    task_progress_id INT AUTO_INCREMENT PRIMARY KEY,    -- Unique ID for each progress update
    task_id INT NOT NULL,                                -- Foreign Key referencing Tasks table
    user_id INT NOT NULL,                                -- Foreign Key referencing Users table (the assignee)
    status VARCHAR(50) NOT NULL,                         -- Status of the task (In Progress, Completed, Blocked)
    progress_percentage INT NOT NULL,                    -- Percentage of task completion (0-100%)
    updated_on TIMESTAMP DEFAULT CURRENT_TIMESTAMP,      -- Timestamp for when the progress was last updated
    comments TEXT,                                       -- Comments or blockers (optional)
    estimated_time_remaining INT,                        -- Estimated remaining time in hours/days (optional)
    FOREIGN KEY (task_id) REFERENCES Tasks(task_id),    -- Assuming Tasks table exists
    FOREIGN KEY (user_id) REFERENCES Users(user_id)     -- Assuming Users table exists
);


CREATE TABLE Reports (
    report_id INT AUTO_INCREMENT PRIMARY KEY,           -- Unique ID for the report
    generated_by INT NOT NULL,                           -- User ID of the manager who generated the report
    report_type VARCHAR(100) NOT NULL,                   -- Type of the report (e.g., Task Progress, Team Performance)
    date_range_start DATE NOT NULL,                      -- Start date of the report period
    date_range_end DATE NOT NULL,                        -- End date of the report period
    task_filter VARCHAR(50),                             -- Filter for task status (Completed, Pending, Overdue)
    employee_filter INT,                                 -- Filter by employee (optional)
    skill_filter INT,                                    -- Filter by skill (optional)
    created_on TIMESTAMP DEFAULT CURRENT_TIMESTAMP,      -- Timestamp for when the report was generated
    report_data TEXT,                                    -- Data of the report in JSON or CSV format
    status VARCHAR(50) DEFAULT 'Generated',              -- Report status (Generated, Pending, Archived)
    FOREIGN KEY (generated_by) REFERENCES Users(user_id) -- Assuming Users table exists
);

