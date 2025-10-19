# Wordpress Docker Local Development Template

## Overview

This is a reusable Docker Compose template for local WordPress development. It provides a containerized environment for building WordPress projects (blogs, e-commerce stores, etc.) without installing software directly on your machine.
The template uses Docker's official WordPress image and MySQL to create isolated, reproducible development environments. Each new WordPress project gets its own container setup, meaning sites don't interfere with each other and you can run multiple projects simultaneously on different ports.

## Why this approach?

- No software installation: No need to install WordPress, PHP, MySQL, or web servers on your machine
- Reusable: Clone this template for each new project; no need to rebuild infrastructure every time
- Isolated environments: Each WordPress project runs in its own containers with its own database
- Consistent setup: Every project uses the same Docker configuration, making workflows predictable
- Easy to manage: Simple folder structure and environment variables let you customize each project without touching the core setup
- Production-like: Mirrors how WordPress typically runs in production (separate services for web, PHP, and database)

## Project structure

```
wordpress-docker-template/
├── docker-compose.yml     # Defines all Docker services and configuration
├── .env                   # Environment variables for project-specific settings
├── .gitignore             # Git rules to exclude unnecessary files
├── wordpress/             # Where WordPress files and content are stored
├── db/                    # Where MySQL database files are persisted
└── README.md              # This file
```

## Key directories

- wordpress/ — Contains WordPress installation, themes, plugins, and uploaded files. Mounted from your host machine so changes persist after containers stop.
- db/ — Contains MySQL database files. Mounted from your host machine to ensure your database survives container restarts.

## How to use this template?

### For a new project

1. Clone this repository into a new directory with your project name:
```
git clone <this-repo-url> my-wordpress-site
cd my-wordpress-site
```

2. Customize the `.env` file with project-specific settings:
- Database name
- Port number (if running multiple projects, use different ports)
- Wordpress site title and other preferences

3. Start the containers
```
docker-compose up -d
```

4. Access your wordpress site in your browser at `http://localhost:<PORT>` (using the port set in the `.env`).

5. Begin development - install plugins, create content, build your site as you normally would.

### Stopping and starting

- Stop containers: ```docker-compose down```
- Start again later: ```docker-compose up -d``` (your data persists)
- Remove everything: ```docker-compose down -v``` (warning: deletes data)

## Component purposes

### docker-compose.yml

Defines all services your WordPress site needs:

- WordPress service — The official WordPress image containing PHP and WordPress
- MySQL service — The database that stores all WordPress data
- Nginx/Web server (if included) — Handles HTTP requests and routes them to WordPress

The file specifies volume mounts (linking `wordpress/` and `db/` folders), environment variables, port mappings, and networking so services can communicate.

### .env File

Contains environment variables that customize each project:

- Database credentials (name, user, password)
- WordPress admin password
- Site URL and title
- Port number
- Any other project-specific settings

This keeps sensitive information and configuration separate from the Compose file, making the template reusable without modification.

### wordpress/ Directory

Mounted volume where WordPress files live. After first run, this contains:

- WordPress core files
- Installed themes
- Installed plugins
- Uploaded media and content

Because it's mounted from your host machine, you can browse and edit files directly, and changes persist even when containers are stopped.

### db/ Directory

Mounted volume where MySQL stores database files. This ensures your WordPress database (posts, pages, users, settings, etc.) persists between container restarts and is backed up on your machine.

## Standard Workflow

1. Clone template for new project
2. Update .env with project details
3. Run docker-compose up -d
4. WordPress is ready; install standard plugins as needed (WooCommerce, etc.)
5. Build your site using WordPress admin interface
6. Stop with docker-compose down when done
7. Restart later with docker-compose up -d — all your work is preserved

##No Custom Code Required

This template uses standard WordPress and official Docker images. It's designed for developers who want to focus on WordPress configuration and content, not on building custom PHP or managing complex infrastructure. Standard plugins (WooCommerce, payment gateways, email services) integrate normally.

## Technical Details

### Data Persistence (Bind Mounts)

This template uses bind mounts rather than Docker volumes for the `wordpress/` and `db/` directories. Bind mounts link Docker containers directly to folders on your host machine, which means:

- You can browse, edit, and back up WordPress files and database files directly on your computer
- Your data persists even when containers are stopped or removed
- You have full transparency and control over your development data
- This is the recommended approach for local development workflows

### Environment Variables and .env

Docker Compose automatically reads the `.env` file in the same directory as `docker-compose.yml`. Any variable in the Compose file formatted as `${VARIABLE_NAME}` gets replaced with the corresponding value from `.env`.
For example, `${DB_NAME}` in the Compose file is automatically replaced with the `DB_NAME` value you set in `.env`. This happens automatically—no additional configuration needed. Just ensure the `.env` file is in the project root alongside `docker-compose.yml`.

## General Notes

- Email, payments, encryption, and other integrations use sandbox/test modes in development (real credentials go in production)
- Each new project should use a different port number in `.env` to avoid conflicts
- Database and WordPress files in `wordpress/` and `db/` folders should be in `.gitignore` — they're local development data, not version-controlled
- Health checks ensure the database is ready before WordPress attempts to connect, preventing startup errors
