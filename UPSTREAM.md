# Upstream

| | |
| --- | --- |
| Project | AWSGoat |
| Repository | https://github.com/ine-labs/AWSGoat |
| Version | master (no releases) |
| Commit | b24869ad455ed8d1393d00ecdc15ee638d1c1332 |
| Licence | MIT |

`app/` is that commit, unchanged, without its Git history (the whole AWSGoat repository; this lab
applies its Terraform root module `app/modules/module-2/` directly, as upstream's GitHub Actions
workflow does). Upstream's deploy steps (`local-exec`, run with `/bin/bash`) write the database
address into `resources/ecs/task_definition.json` with `sed -i`, then put the placeholder back.
Current Terraform rejects that write between plan and apply (`file()` returned an inconsistent
result), hence the second `isoloom run` the README describes. To update, replace `app/` with a newer commit and change this table.
