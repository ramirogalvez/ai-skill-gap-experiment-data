# Rúbrica general
[turn 1]
I need to mass review / evaluate some human responses to a task of answering to some sample emails using the provided reports as context. The tasks are assigned to high school students near graduation to assess workplace skills competency.



The three tasks have been attached. The preregistered evaluation criteria is as follows:

*Content* (0-10 points):
-Diagnosis (6 points): Up to 2 points per question for each of the 3 questions posed by
the business manager/owner.
-Solution (4 points): 1 point for addressing the root cause of the problem, up to 2 points
for making a concrete and specific proposal, and 1 point for realism. If the solution does
not address the root cause, the respondent scores 0 for the solution subsection.

*Writing* (0-10 points):
-Spelling and grammar (2 points)
-Clarity and legibility (3 points)
-Organization (4 points)
-Tone and register (1 point)

One problem I've faced in the past is that LLM quantitative assessments / grading tend to collapse in a very narrow range of integers {6, 7, 8}. The range is not a problem - answers could always be normalized / standardized, but the lack of resolution to distinguish answers of different qualities is. What are the best practices, currently, for employing state of the art LLMs (GPT-5.1 thinking level) for this task?

The trial's design documents have been uploaded to the project folder.

[turn 2]
Fully develop this into a grading guide, with additional criteria (we must respect the pre-registered criteria but we can add further breakdowns, plus instructions on how to assign the scores. For example, what does an answer need to have, or how does it need to be, to merit each of the five different levels for 'organization'? ( {0, 1, 2, 3, 4} )

We need a fully detailed rubric for each trait / dimension that describes what will get graded with each level, as in a fully specified procedure or checklist. The grading guide / rubric might (or perhaps should) contain additional traits / features / characteristics to assist in the evaluation, such as 'assign zero to the diagnostic trait if there are gross math errors', etc.

The grading guide for each trait must make sense in the context of the trial, and regarding the target population which is being assessed.

[turn 3]
Based on the extremely detailed rubric that we discussed earlier, create a prompt plus schema. Return the schema as YAML. I'm going to use simonw's llm cli tool. Assume that we will pass the general instructions as system prompt and then the human's response to be evaluated as the general prompt. (I'm open to suggestions for doing it differently.) 

