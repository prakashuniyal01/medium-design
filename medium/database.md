## user tables 
    -- Table to store user roles, such as admin, editor, journalist, and pending
    CREATE TABLE roles (
        role_id SERIAL PRIMARY KEY,
        role_name VARCHAR(20) NOT NULL UNIQUE CHECK (role_name IN ('admin', 'editor', 'journalist', 'pending'))
    );

    -- Main users table, including a role reference and fields for approval
    CREATE TABLE users (
        user_id SERIAL PRIMARY KEY,
        username VARCHAR(150) NOT NULL UNIQUE,
        full_name varchar(255) NOT NULL,
        password_hash VARCHAR(255) NOT NULL,
        email VARCHAR(255) UNIQUE,
        role_id INT REFERENCES roles(role_id) DEFAULT (SELECT role_id FROM roles WHERE role_name = 'pending'),
        is_approved BOOLEAN DEFAULT FALSE, -- True if approved by an admin
        created_at TIMESTAMP DEFAULT NOW(),
        updated_at TIMESTAMP DEFAULT NOW(),
        
        -- Only one admin allowed
        CONSTRAINT unique_admin CHECK (
            (SELECT COUNT(*) FROM users WHERE role_id = (SELECT role_id FROM roles WHERE role_name = 'admin')) <= 1
        )
    );

    -- Table to manage requests for users seeking role changes (e.g., becoming an editor or journalist)
    CREATE TABLE role_requests (
        request_id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(user_id) ON DELETE CASCADE,
        requested_role_id INT REFERENCES roles(role_id), -- The desired role, either 'editor' or 'journalist'
        status VARCHAR(20) CHECK (status IN ('pending', 'approved', 'denied')) DEFAULT 'pending',
        request_date TIMESTAMP DEFAULT NOW(),
        approved_by INT REFERENCES users(user_id) -- Administrator who approves or denies the request
    );

    CREATE TABLE password_reset_tokens (
        token_id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(user_id) ON DELETE CASCADE,
        reset_token VARCHAR(255) UNIQUE NOT NULL,
        expiration_time TIMESTAMP NOT NULL DEFAULT (NOW() + INTERVAL '2 minutes'),
        is_used BOOLEAN DEFAULT FALSE,
        created_at TIMESTAMP DEFAULT NOW()
    );




## articles 
    -- Table to store article statuses, including draft, submitted, published, rejected, and deleted
    CREATE TABLE article_status (
        status_id SERIAL PRIMARY KEY,
        status_name VARCHAR(20) NOT NULL UNIQUE -- 'draft', 'submitted', 'published', 'rejected', 'deleted'
    );

    -- Articles table, modified to include a status_id reference
    CREATE TABLE articles (
        article_id SERIAL PRIMARY KEY,
        title VARCHAR(255) NOT NULL,
        content TEXT NOT NULL,
        author_id INT REFERENCES users(user_id) ON DELETE CASCADE,
        status_id INT REFERENCES article_status(status_id) DEFAULT (SELECT status_id FROM article_status WHERE status_name = 'draft'),
        created_at TIMESTAMP DEFAULT NOW(),
        updated_at TIMESTAMP DEFAULT NOW()
    );

    -- Table to track requests to delete or reject an article
    CREATE TABLE article_requests (
        request_id SERIAL PRIMARY KEY,
        article_id INT REFERENCES articles(article_id) ON DELETE CASCADE,
        user_id INT REFERENCES users(user_id) ON DELETE CASCADE,
        request_type VARCHAR(20) CHECK (request_type IN ('delete', 'reject')),
        status VARCHAR(20) CHECK (status IN ('pending', 'approved', 'denied')) DEFAULT 'pending',
        request_date TIMESTAMP DEFAULT NOW(),
        approved_by INT REFERENCES users(user_id) -- Admin who approves or denies the request
    );

    -- Table to log changes in article status
    CREATE TABLE article_logs (
        log_id SERIAL PRIMARY KEY,
        article_id INT REFERENCES articles(article_id) ON DELETE CASCADE,
        action VARCHAR(50) NOT NULL, -- 'created', 'updated', 'submitted', 'published', 'rejected', 'deleted'
        performed_by INT REFERENCES users(user_id),
        action_date TIMESTAMP DEFAULT NOW()
    );

    -- Table to store unique tags
    CREATE TABLE tags (
        tag_id SERIAL PRIMARY KEY,
        tag_name VARCHAR(50) NOT NULL UNIQUE
    );

    -- Intermediate table to establish a many-to-many relationship between articles and tags
    CREATE TABLE article_tags (
        article_id INT REFERENCES articles(article_id) ON DELETE CASCADE,
        tag_id INT REFERENCES tags(tag_id) ON DELETE CASCADE,
        PRIMARY KEY (article_id, tag_id) -- Composite primary key
    );


## like and comments 
   -- Table to store likes on articles
    CREATE TABLE likes (
        like_id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(user_id) ON DELETE CASCADE,
        article_id INT REFERENCES articles(article_id) ON DELETE CASCADE,
        created_at TIMESTAMP DEFAULT NOW(),
        UNIQUE (user_id, article_id) -- Ensure a user can like an article only once
    );

    -- Table to store likes on comments
    CREATE TABLE comment_likes (
        comment_like_id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(user_id) ON DELETE CASCADE,
        comment_id INT REFERENCES comments(comment_id) ON DELETE CASCADE,
        created_at TIMESTAMP DEFAULT NOW(),
        UNIQUE (user_id, comment_id) -- Ensure a user can like a comment only once
    );

    -- Table to store comments on articles, allowing replies to comments
    CREATE TABLE comments (
        comment_id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(user_id) ON DELETE CASCADE,
        article_id INT REFERENCES articles(article_id) ON DELETE CASCADE,
        content TEXT NOT NULL,
        parent_comment_id INT REFERENCES comments(comment_id) ON DELETE CASCADE, -- Self-reference for replies
        created_at TIMESTAMP DEFAULT NOW()
    );


    

### API Request Log Table
    CREATE TABLE api_request_logs (
        log_id SERIAL PRIMARY KEY,
        user_id INT REFERENCES users(user_id) ON DELETE SET NULL, -- Nullable for unauthenticated requests
        ip_address VARCHAR(45) NOT NULL,                          -- Stores the IP address (IPv4 or IPv6 compatible)
        endpoint VARCHAR(100) NOT NULL,                           -- API endpoint accessed
        request_method VARCHAR(10) NOT NULL,                      -- GET, POST, PUT, DELETE, etc.
        request_count INT DEFAULT 1,                              -- Tracks the number of times this IP accessed the endpoint
        is_authenticated BOOLEAN DEFAULT FALSE,                   -- Indicates if the request was authenticated
        isp_name VARCHAR(100),                                    -- Name of the ISP
        region VARCHAR(100),                                      -- Region or approximate location
        network_type VARCHAR(50)                                  -- Type of network (e.g., WiFi, Mobile, Broadband)
        created_at TIMESTAMP DEFAULT NOW()
    );

