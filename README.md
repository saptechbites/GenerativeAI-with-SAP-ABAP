Organizations that use SAP are familiar with flexibility as well as challenges associated with custom code. According to SAP’s review, [65% of custom code has not been used](https://news.sap.com/2024/06/sap-sapphire-sap-customers-drive-business-transformation/) in past 12 months by several organizations. So, with SAP’s direction for organizations to move to a Clean Core, this is the right time to evaluate your custom code and understand the impact and opportunity going forward.

With considering move to S/4HANA and Clean Core, SAP customers are looking at following areas as priorities:

- **SAP Custom code understanding**: people who originally wrote the code may not be accessible anymore and robust documentation doesn’t exist, so understanding the custom is one of the first steps towards a clean core future
- **Custom code decommissioning**: purging code that is either not used anymore or for functionality that comes standard with S4HANA
- **Code transformation**: transform SAP ECC code to S4HANA and decide which tools and technologies to use for extensions
- **Code Optimization**: optimize for performance, efficiency, or resources
- **Code Review for best practices**: ensuring new code meets organizational coding best practices, does not have any sensitive information, and follows SAP ABAP Cloud guidelines such as access only SAP Public APIs

Without talking about any company or model, we wanted to focus on whether Generative AI models are positioned to help with any of the above. With the disclaimer that _Gen AI can hallucinate, and general training doesn’t imply that it would understand SAP ABAP code, we think of this as an experiment to talk about direction rather thanks absolute solution_. So, we don’t recommend relying solely on the results of Gen AI and always have a human in the loop.

Gartner also has an insightful article that talks about [when not to use Generative AI](https://www.gartner.com/en/articles/when-not-to-use-generative-ai).

<br>

## Definitions 

This section defines some of the Generative AI related terminologies we’ll be using in this article

- **Foundation Models (FM):** Machine learning models that are trained on large amount of general unlabeled data set and can perform wide variety of tasks such as new text and image generation and natural language conversation. Transformer model used in Generative AI is capable of learning context and sequence of words.
- **Prompt**: A question or set of instructions that you provide to AI to generate usable answer from an FM
- **Hallucination:** Whether an FM knows the right answer or not, it produces some answer; hallucination is when it generates inaccurate or inconsistent answer
- **Retrieval Augmented Generation (RAG)**: It is a way of grounding the response of the model by providing a knowledge base as input along with the prompt

<br>

## Prerequisites

To test the use cases highlighted in this article, you can use any IDE and connect it to an FM such as Visual Studio (VS) code with [Open Source Continue addon](https://marketplace.visualstudio.com/items?itemName=Continue.continue).


**Prompt’s impact on output**

You may already be familiar that the prompts guide the behavior of the FM and leading providers of FM such as [Anthropic](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview) and [OpenAI](https://help.openai.com/en/articles/6654000-best-practices-for-prompt-engineering-with-the-openai-api) have published guidance about it.

So, we know that prompts matter and can be used to refine and customize the output. This article uses this idea to explore persona-based ABAP code documentation.

<br>

## Persona based code documentation

If you are an ABAP programmer, you want more detailed explanation of the code including each class or function vs program manager who wants to understand what the code does at a higher level.

Examples of code explanation based upon these 2 personas (code is supplied as part of prompt)

### Program Manager Persona:

    Prompt: Following is a custom SAP ABAP code. The person who wrote this left the organization and we are trying to figure out what this code does.

    Output: Here's a summary of what this custom SAP ABAP code does:

    1. It's a report named ZPREDACCT_MOVE_RECORDS that handles data movement for predictive accounting.
    2. The program takes a date input (FOR_DATE) from the user.
    3. It checks if predictive accounting is enabled by querying the ZPA_SWITCH table.
    4. If enabled, it retrieves percentage data from ZPA_PERCENTAGE table for the current month and next 6 months.
    5. Based on the input date, it determines the current and next month for processing.
    6. For each month (current + 6 future months), it:
     a. Retrieves data from ZDEMO_DATA table for that month.
     b. Calculates how many records to move based on percentages from ZPA_PERCENTAGE.
     c. Checks if records have already been moved for that month in ZPREDACCT_DATA.
     d. Randomly selects the calculated number of records from ZDEMO_DATA.
     e. Moves these records to ZPREDACCT_DATA and updates their status in ZDEMO_DATA.
    7. After processing all months, it updates both ZPREDACCT_DATA and ZDEMO_DATA tables.
    8. The program includes a helper function GET_NEXT_PERIOD to calculate next month's dates.

    In essence, this program moves a configured percentage of records from ZDEMO_DATA to ZPREDACCT_DATA for predictive accounting purposes, doing so for the current month and 6 months into the future. The percentages vary by month based on the ZPA_PERCENTAGE table configuration


<br>

> **_Tip_**_: Based on above output, it wouldn’t be a stretch to suggest that it can also be used to evaluate whether the code is needed going forward or not, thus accelerating your_ **_clean core_** _initiative._

<br>

### ABAP Programmer Persona:

    Prompt: You are an SAP ABAP coding expert, and following is a custom SAP ABAP code. The person who wrote this left the organization and we are trying to understand what this code does. The result will be reviewed by a programmer, so provide detailed technical output.
    
    Output: This ABAP code appears to be a report program named ZPREDACCT_MOVE_RECORDS. Its main purpose is to move records from a source table (ZDEMO_DATA) to a target table (ZPREDACCT_DATA) for predictive accounting purposes. Here's a detailed breakdown of what the code does:
    
    1. Program Initialization:
       - Defines a selection screen with a parameter FOR_DATE (default is current date).
       - Declares various internal tables and structures.
    
    2. Selection Screen Validation:
       - Checks if the entered date is not in the future.
    
    3. Main Processing:
       - Checks if Predictive Accounting (PA) is enabled by querying the ZPA_SWITCH table.
       - If enabled, it proceeds with the following steps:
    
    4. Date Calculations:
       - Determines the current period and processing date based on the input date.
       - Calculates future months (up to 6 months ahead).
    
    5. Percentage Retrieval:
       - Fetches percentage values for each future month from the ZPA_PERCENTAGE table.
    
    6. Record Processing (for each future month):
       - Retrieves records from ZDEMO_DATA for the specific month.
       - Calculates the number of records to be moved based on the percentage.
       - Checks for existing records in ZPREDACCT_DATA for the same period.
       - Randomly selects records from ZDEMO_DATA to be moved, up to the calculated number.
       - Moves selected records to ZPREDACCT_DATA, updating their status in ZDEMO_DATA.
    
    7. Data Updates:
       - Updates ZPREDACCT_DATA with the new records.
       - Updates ZDEMO_DATA to mark processed records.
    
    8. Helper Function:
       - FORM get_next_period: Calculates the next period and its start/end dates.
    
    Key Points:
    - The program uses several custom tables: ZDEMO_DATA, ZPREDACCT_DATA, ZPA_SWITCH, ZPA_PERCENTAGE.
    - It implements a predictive accounting scenario, moving a percentage of records to a future period.
    - The process is repeated for up to 6 future months.
    - Random selection is used to choose which records to move.
    - The code includes both interactive and batch processing logic.
    
    Potential Improvements:
    1. Error handling could be enhanced, especially for database operations.
    2. The code could benefit from modularization into smaller, more manageable functions.
    3. Some hardcoded values (like 6 for future months) could be made configurable.
    4. Performance optimization might be needed for large datasets.
    
    This code appears to be part of a custom predictive accounting solution, likely used for financial forecasting or planning purposes within an SAP system.

<br>

## Code review against organization’s standards

You can use this technique as a quality check for code review by supplying both code and your organizational guidance. Depending on your choice of tool, you can either upload these documents or provide as a prompt.

    Prompt: You are SAP ABAP Coding guide. File ZPREDACCT_MOVE_RECORDS.txt has the SAP ABAP Code and file Org-Standard-guidance.pdf has the organization guideline. Can you review the code against the guideline and provide the compliance report
    
    Output: Based on the review of the ABAP code in ZPREDACCT_MOVE_RECORDS.txt and the organizational guideline in Org-Standard-guidance.pdf, here is the compliance report:
    
    1. Naming Conventions:
       - The program name ZPREDACCT_MOVE_RECORDS follows the standard naming convention for customer programs (starting with Z).
       - Variable names like ls_sales_order, lt_sales_order, lt_dbupdate_so, etc. follow the standard naming conventions for local and internal tables.
    2. Code Structure:
       - The code is well-structured with proper event blocks (START-OF-SELECTION, AT SELECTION-SCREEN, etc.).
       - The code is modularized with the use of FORM routines (e.g., get_next_period).
    3. Comments:
       - The code has adequate comments explaining the purpose and functionality of the program, as well as individual sections and routines.
    4. Data Declarations:
       - Data types are explicitly defined for variables (e.g., TYPE dats, TYPE zdemo_data, etc.).
       - Internal tables are declared with proper data types (TYPE TABLE OF abaplist, TYPE TABLE OF zdemo_data, etc.).
    5. Error Handling:
       - The code includes error handling and logging mechanisms using MESSAGE statements.
       - Appropriate system fields (sy-subrc) are checked for errors.
    6. Performance:
       - The code appears to be following best practices for performance, such as using internal tables, avoiding unnecessary loops, and utilizing SAP standard functions (e.g., CALL FUNCTION).
    7. Coding Standards:
       - The code follows the SAP coding standards for indentation, blank lines, and code formatting.
       - The code uses SAP standard constructs and statements (e.g., DATA, TYPE, PARAMETERS, CALL FUNCTION, etc.).
    8. Alignments with Functional Requirements:
       - The code appears to be aligned with the functional requirements outlined in the Org-Standard-guidance.pdf guideline.
       - The code includes functionality for calculation, posting, and reversal, as well as portfolio movements and other specific requirements mentioned in the guideline.
    Overall, the ABAP code in ZPREDACCT_MOVE_RECORDS.txt generally complies with the organizational guideline provided in Org-Standard-guidance.pdf. The code follows SAP coding standards, includes proper error handling and logging, and aligns with the functional requirements outlined in the guideline. However, it's important to note that a comprehensive code review would require additional checks, such as testing, code coverage analysis, and adherence to specific organizational coding guidelines (if any), which are not available in the provided files.

<br>

## Improving output and accuracy

The outputs shown above are examples of what an output may look like, changing based upon your persona’s prompt. There are several techniques to improve the output, as follows:

- **Prompting techniques**: Improving prompts using techniques such as [multi-shot prompting](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/multishot-prompting) (providing example to the model) has shown to provide better output and reduce hallucinations. Experiment with different prompts to find out which one matches your use case the best.

- **Model Evaluation**: Evaluating the output of different FMs allows you to not only select the best model for the use case, but also decide about tradeoffs among output, price, and latency. [Anthropic provides an evaluation tool](https://docs.anthropic.com/en/docs/test-and-evaluate/eval-tool) allowing you to test different prompts, which you can use for metrics such as accuracy, robustness, and safety.
- **Model Customization**: this is a technique that lets you customize general FMs with your own data for specific domain. Model customization is generally time and cost intensive and we recommend extensive testing using prompting and model evaluation before you think about this option

<br>

## What’s next

There is a lot more that goes into solving a real-world problem than this article talks about, and we wanted to provide our point of view about where and how to get started and how to think about using Gen AI for SAP Code.

Feel free to add your feedback and experience in the comments!
