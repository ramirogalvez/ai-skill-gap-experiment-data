# ai-skill-gap-experiment-data

This repository contains the **system prompts**, **task-specific grading rubrics**, and **independent-researcher materials** used for the **AI-assisted scoring pipeline** in our AI productivity-gap experiment.

These files are provided for **transparency** and **replicability**: they document exactly what was shown to the grading models (system prompts) and the complete rubric specifications used to score each task version.

## Repository structure

```text
main-pipeline/
  system-prompts/
    task_grading_system_prompt.md
    followup_grading_system_prompt.md
  rubrics/
    task/
      rest_task_guides.json
      lunch_task_guides.json
      sales_task_guides.json
    followup/
      rest_fup_guides.json
      lunch_fup_guides.json
      sales_fup_guides.json

independent-researcher/
  strategy-1/
    gpt-5.1-pro-generic-rubric-prompts.md
    grader_prompt_v1.md
  strategy-2/
    gpt-5.1-pro-detailed-rubric-prompts.md
    grader_prompt_v2.md
    grader_schema.yml
    task-materials/
      lunch/
        detailed_task_facts_lunch.md
        detailed_task_rubric_lunch.md
      rest/
        detailed_task_facts_rest.md
        detailed_task_rubric_rest.md
      sales/
        detailed_task_facts_sales.md
        detailed_task_rubric_sales.md
  strategy-3/
    elo_prompt.md
    elo_schema.yml
```

## What each folder contains

### `main-pipeline/`

Inputs to the **main AI-assisted grading pipeline** used in the paper.

* `main-pipeline/system-prompts/`

  * `task_grading_system_prompt.md`: system prompt used to grade **main task** responses.
  * `followup_grading_system_prompt.md`: system prompt used to grade the **follow-up open-ended** response.

* `main-pipeline/rubrics/`

  * `task/*_task_guides.json`: task-specific JSON rubrics for the **main task**.
  * `followup/*_fup_guides.json`: task-specific JSON rubrics for the **follow-up** open-ended question.

Each JSON rubric includes the full task context (email + attached report components) and the detailed scoring instructions used by the grading model.

### `independent-researcher/`

Materials used by an **independent researcher** (not involved in the main pipeline development) to implement alternative AI-assisted grading strategies for robustness.

* `strategy-1/`: single-step grading using an LLM-generated rubric and evaluator prompt.
* `strategy-2/`: same rubric approach augmented with task-specific fact sheets and a YAML output schema.
* `strategy-3/`: pairwise-comparison approach aggregated using an Elo rating system (prompt + YAML schema).

## Mapping of task versions

Filename prefixes correspond to the three task contexts used in the experiment:

* `rest_*`  : food delivery task version
* `sales_*` : home appliances store task version
* `lunch_*` : cafeteria task version
