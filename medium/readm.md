zusp ajkr gorm sfxt

## ui refrances the websites 
    - https://htmldesigntemplates.com/html/magberg/?storefront=envato-elements#


## overview 
    - The backend will handle user authentication, article management, and API integration.
    - Scenario: You have been hired by a media company to develop an internal article management system. Journalists, editors, and administrators will use the system to create, edit, review, and publish articles. The system also includes an API to allow integration with third-party platforms.

    You need to build the following features in the system:

    Part 1: Article Submission Form
    The first task is to create a two-page form that allows journalists to submit articles for review.

    Form Fields:

    Page 1:
    Title (Text Field)
    Subtitle (Text Field)
    Article Content (Text Area)
    Author Name (Text Field)
    Email Address (Email Field)
    
    Page 2:
    Article Image (File Upload)
    Tags (Checkboxes for predefined tags like Politics, Sports, Tech, etc.)
    Category (Dropdown with categories like News, Opinion, Feature)
    Publish Date (Date Field, must be a future date)
    Agree to Terms (Checkbox)
    Custom Validation:

    Ensure the title is at least 10 characters long.
    Validate that the publish date is in the future.
    Email field must have a valid format.
    Ensure that the terms checkbox is checked.
    Pagination:

    Implement pagination to split the form across two pages, with the ability to navigate between pages without losing form data.
    Error Handling:

    Display validation errors with user-friendly messages.
    Part 2: Article Management System (Backend & API)
    The second part involves building the backend for managing articles and users, including authentication, CRUD operations, and API integration.

    Authentication:

    Implement user registration, login, and logout functionality.
    Use Django's built-in authentication system.
    Allow users to reset passwords via email.
    Authorization:

    Three user roles:
    Journalist: Can submit and edit their own articles but cannot publish them.
    Editor: Can review, approve, and publish articles.
    Admin: Has full access to create, edit, delete, and manage users.
    Use Django’s permission system to restrict actions based on roles.
    CRUD Operations:

    Create a model called Article with fields such as title, content, author, image, tags, category, and publish_date.
    Journalists can create and edit their own articles.
    Editors can approve, reject, or publish articles.
    Admins can manage articles and users.
    Django Rest Framework (DRF) API:

    Create an API for the Article model using DRF.
    Implement CRUD operations via the API.
    Use Token Authentication for the API.
    Restrict API access based on user roles (e.g., only Editors and Admins can approve or publish articles).
    Implement pagination, search, and filtering in the API (e.g., filter articles by category or search by title).

    Bonus Features:

    Implement email notifications when an article is submitted for review and when it is approved or rejected.
    Create an endpoint for retrieving a list of published articles, accessible to the public.

    Final Submission:
    The trainees will submit their project as a GitHub repository with the following:

    A detailed README.md with setup instructions.
    Fully functional code with all features implemented.

