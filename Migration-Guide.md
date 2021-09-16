# File structure changes after migratio
Once migration success, The file structure has changed. </p>
there generates new folder</p> **configs**</p> **migrationbackup**</p> **publishProfiles**</p> under .fx folder
Under project root path, there generates new folder named **"templates"**, this folder stores ARM bicep files.
# Backup
We suggest you backup manually before migration.</p>
toolkit helps to backup env.default.json file under .fx/migrationbackup folder
# Must-do steps after migration success
After migration, you must provision again. </p>
if project uses bot, we will create a new bot for you.</p>
if project uses tab, we will generate new tab resource for you.


