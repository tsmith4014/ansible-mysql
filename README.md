---

# Ansible Playbook Guide for MySQL Setup

This README provides comprehensive instructions on running the `pbsql.yml` Ansible playbook for MySQL setup, including database creation and enabling remote login.

## Prerequisites

- Ansible installed on your system.
- Access to target servers (defined in your Ansible inventory).
- Sufficient privileges on target servers.

## Structure of the Playbook

- `pbsql.yml` - Main playbook file.
- `vars_linux2.yml` - Variable file with MySQL configurations.

## Environment Setup

1. **Define Your Inventory:** Create or use an existing Ansible inventory file.
2. **Setup Environment Variables:** Ensure the `env` value matches the corresponding `vars_<env>.yml` file.
3. **Variable File (`vars_linux2.yml`):** Customize MySQL packages, service names, database user, password, and database name.

## Running the Playbook

### Basic Command

```bash
ansible-playbook -i <inventory_file> pbsql.yml -e "env=<environment>"
```

Replace `<inventory_file>` with your inventory file path and `<environment>` with your environment identifier.

### Specific Example Command

```bash
ansible-playbook -i hosts pbsql.yml -e "env=linux2" -i inventory
```

```bash
ansible-playbook pbsql.yml -e "env=ubuntu" -i inventory

```

This command uses the `hosts` inventory file and sets the environment to `linux2`.

## Tasks Explained

1. **Include Environment Variables:** Dynamically includes variables based on the environment.
2. **Install MySQL and Dependencies:** Installs MySQL and dependencies from the variable file.
3. **Start and Enable MySQL Service:** Ensures MySQL service is running and enabled.
4. **Create MySQL User:** Creates a MySQL user with specified privileges.
5. **Create Database:** Creates a database with the specified name.
6. **Enable Remote Login:** Modifies MySQL configuration for remote connections.

## Handlers

### Understanding Handlers

- Handlers are triggered by tasks and typically used for restarting services or performing cleanup operations.
- In this playbook, the handler is used to restart the MySQL service.

### Example Handler

```yaml
handlers:
  - name: Restart MySQL
    service:
      name: mysql
      state: restarted
```

This handler restarts the MySQL service when invoked by a task.

## Top 10 Ansible Methods/Functions

1. **ansible-playbook**: Executes Ansible playbooks.
2. **include_vars**: Includes variables from a file.
3. **package**: Manages packages on the target hosts.
4. **service**: Manages services on the target hosts.
5. **mysql_user**: Manages MySQL users.
6. **mysql_db**: Manages MySQL databases.
7. **lineinfile**: Edits lines in a text file.
8. **copy**: Copies files to remote locations.
9. **debug**: Prints statements during execution.
10. **template**: Templates a file out to a remote server.

## Understanding the 'item' Keyword

In the context of the playbook, `item` is used within a loop to represent each element in a list. Here's how it works:

```yaml
- name: Installing MySQL and dependencies
  package:
    name: "{{ item }}"
    state: present
    update_cache: yes
  loop: "{{ mysql_packages }}"
```

- `mysql_packages` is a list of packages defined in `vars_linux2.yml`.
- The `loop` keyword tells Ansible to iterate over this list.
- In each iteration, `item` represents the current package from the list.
- This allows the task to install each package defined in `mysql_packages`.

## Note on Security

- Ensure proper firewall rules to restrict MySQL access.
- Use strong, unique passwords for database users.

## Troubleshooting

- Use `-vvv` for verbose mode.
- Check playbook syntax with `ansible-playbook --syntax-check`.
- Verify all required variables are correctly formatted.


Certainly! I'll add a section to the README that provides a list of useful MySQL commands. These commands will help you check and manage the database that your Ansible playbook creates.

---

## MySQL Database Management Commands

After your Ansible playbook has set up the MySQL database, you can use the following MySQL commands to manage and verify the database and its contents. Make sure you have MySQL client installed on your system to run these commands.

### Accessing MySQL

```bash
mysql -u [username] -p
```

Replace `[username]` with the MySQL user. You'll be prompted to enter the password.

### Show Databases

```sql
SHOW DATABASES;
```

Lists all databases on the MySQL server.

### Using a Database

```sql
USE [database_name];
```

Replace `[database_name]` with the name of your database.

### Show Tables

```sql
SHOW TABLES;
```

Displays tables in the current database.

### Describe a Table

```sql
DESCRIBE [table_name];
```

Replace `[table_name]` with the name of the table to see its structure.

### Select Data from a Table

```sql
SELECT * FROM [table_name];
```

Replace `[table_name]` with the name of the table to view its data.

### Create a New User

```sql
CREATE USER '[username]'@'localhost' IDENTIFIED BY '[password]';
```

Replace `[username]` and `[password]` with the desired username and password.

### Grant Privileges to User

```sql
GRANT ALL PRIVILEGES ON [database_name].* TO '[username]'@'localhost';
```

Grants all privileges to the user on the specified database.

### Flush Privileges

```sql
FLUSH PRIVILEGES;
```

Reloads the grant tables in the MySQL database, enabling changes to privileges to take effect.

### Exit MySQL

```sql
EXIT;
```

Exits the MySQL command-line tool.

---

This section provides a basic overview of the essential MySQL commands you'll need to interact with the database set up by Ansible. You can expand this section with more specific commands or scripts as needed for your project's requirements.

Yes, Ansible provides various modules specifically designed for managing MySQL databases. These modules allow you to perform a range of tasks such as creating databases, managing users, setting privileges, and more, all within your Ansible playbooks. Here are some of the key Ansible modules for MySQL management:

1. **mysql_db**: This module is used for creating or deleting MySQL databases. It can also be used to dump and import databases.

   Example:

   ```yaml
   - name: Create a MySQL database
     mysql_db:
       name: mydb
       state: present
   ```

2. **mysql_user**: This module manages MySQL user accounts, including creating, updating, and removing users, as well as managing their privileges.

   Example:

   ```yaml
   - name: Create a MySQL user
     mysql_user:
       name: dbuser
       password: password123
       priv: "*.*:ALL"
       state: present
   ```

3. **mysql_variables**: This module manages MySQL global variables.

   Example:

   ```yaml
   - name: Set a MySQL global variable
     mysql_variables:
       variable: max_connections
       value: 150
   ```

4. **mysql_replication**: This module manages MySQL replication.

   Example:

   ```yaml
   - name: Configure slave to use replicated data from master
     mysql_replication:
       mode: changemaster
       master_host: 192.168.1.50
       login_user: root
       login_password: mypass
   ```

5. **mysql_info**: This module provides information about MySQL server configuration.

   Example:

   ```yaml
   - name: Get information about MySQL server
     mysql_info:
       login_user: root
       login_password: mypass
   ```

These modules simplify the process of managing MySQL databases in a programmatic and idempotent way. By incorporating these into your Ansible playbooks, you can automate a wide range of database administration tasks. Remember to install the necessary Python MySQL libraries (like `PyMySQL` or `MySQL-python`) on the host where these modules are run.

---
