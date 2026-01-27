# ai-skill-gap-experiment-data

This repository contains the **full system prompts** and **task-specific JSON rubrics** used for the **AI-assisted grading** pipeline in our GenAI productivity-gap experiment.

## Contents

All grading materials live under:

```text
ai_assisted_grading/
  system_prompts/
    full_system_prompt.md
    full_system_prompt_fup.md
  rubrics/
    main_task/
      rest_task_guides.json
      lunch_task_guides.json
      sales_task_guides.json
    follow_up/
      rest_fup_guides.json
      lunch_fup_guides.json
      sales_fup_guides.json
```

## What each file is

### System prompts

* `ai_assisted_grading/system_prompts/full_system_prompt.md`
  System prompt used to grade the **main task** responses.

* `ai_assisted_grading/system_prompts/full_system_prompt_fup.md`
  System prompt used to grade the **follow-up open-ended** response.

### Task-specific JSON rubrics

* `ai_assisted_grading/rubrics/main_task/*_task_guides.json`
  Rubrics (including full task context + detailed scoring guidelines) for the **main task**.

* `ai_assisted_grading/rubrics/follow_up/*_fup_guides.json`
  Rubrics for the **follow-up** open-ended question.

## Mapping of task versions

Filename prefixes correspond to the three task contexts used in the experiment:

* `rest_*`  : food delivery / hamburguesería task version
* `sales_*` : home appliances store task version
* `lunch_*` : cafeteria task version

