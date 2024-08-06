# ANSIBLE DYNAMIC ASSIGNMENTS (INCLUDE) AND COMMUNITY ROLES

**IMPORTANT NOTICE:** Ansible is an actively developing software project, you can vist Ansible Documentation for the lastest updates on modules and their usuage.

The two previous projects has already helped wuth some knowledge and skills on Ansible, so we canperform configurations using `playbooks`, `roles` and `imports`. Now I will continue with the configurations of the UAT servers, learning and practicing new Ansible concepts and modules. 

I will be introducing `dynamic asssignments` by using `include` module.

The  difference between static and dynamic assignments; static assignments uses `import` Ansible modules, while dynamic assignments uses `include`. Dynamic assignments are more flexible unlike static assignments.

Hence,

```
import = Static
include = Dynamic
```

When the **import**  module is used , all statemnts are pre-processed at the time playbooks are *parsed*. Meaning, when `site.yml` playbook is executed, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that , during the actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when **include** module is used, all statements are processed only during the execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during the execution will be used.

Note, in most cases, it is recommended to use static assignments for playbooks, because it is more reliable. With dynmic , it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for enviroment specific variables , as will be introduced in this project.



## Execution

In my `ansible-config-mgt` Github repository, I started a new and named it `dynanic-assignments`.

![step 1 created a  new branch dynamic assignments](https://github.com/user-attachments/assets/e4842464-741d-4f7d-825c-ed7474a03b87)

Created a new folder and named it `dynamic-assignments`. Then inside this follder, created a new file and named it `env-vars.yml`. I will instruct `site.yml` to `include` this playbook later. For now, I will focus on building the structure. 

My Github repository now has the structure below;

```
├── dynamic-assignments
│   └── env-vars.yml
├── inventory
│   └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
└── roles (optional folder)
    └──...(optional subfolders & files)
└── static-assignments
    └── common.yml
```

Since, I will be using the same Ansible to configure the multiple environments, and each of these will have certain unique attributes, such as **servername, ip-address,** etc, I will need away to set values to variables per spevific environment. So , I would have to create a folder to keep each environment's variables file. Therefore, I will create a new folder `env-vars`, then for each environments create a new YAml files , whic we will use to set variables. 

The layout would now look this :

```
├── dynamic-assignments
│   └── env-vars.yml
├── env-vars
    └── dev.yml
    └── stage.yml
    └── uat.yml
    └── prod.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
├── playbooks
    └── site.yml
└── static-assignments
    └── common.yml
    └── webservers.yml
```





![step 2 envars file created envars file](https://github.com/user-attachments/assets/14aebac5-1b4a-4fb3-b2b9-5afeeb9585af)
