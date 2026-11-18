# Real-Time Language Translation Bot

An interactive, serverless language translation chatbot built using Amazon Lex, AWS Lambda, and Amazon Translate. The chatbot allows users to request translations through natural language conversations and automatically translates their input into their selected target language.

## Project Overview

This project demonstrates how AWS services can be integrated to build a conversational language translation application.

Users interact with an Amazon Lex chatbot by specifying their preferred target language and entering the text they want to translate. AWS Lambda processes the request and invokes Amazon Translate to perform the translation automatically.

The translated result is then returned to the user through the chatbot.

## AWS Services Used

- **Amazon Lex:** Builds the chatbot, manages conversation flow, and collects user input through intents and slots.
- **Amazon Translate:** Automatically translates text into the selected target language.
- **AWS Lambda:** Processes chatbot requests and invokes Amazon Translate to perform translations.
- **AWS IAM:** Manages permissions and securely controls access between AWS services.

## Architecture

The application follows a serverless architecture where Amazon Lex handles user interactions, Lambda processes translation requests, and Amazon Translate performs the actual translation.

![AWS Real-Time Language Translation Bot Architecture](images/Language-Translate-Bot-Topology.png)

### Process Workflow

1. User interacts with the Amazon Lex chatbot.
2. Amazon Lex identifies the user's translation request through the `TranslationIntent`.
3. The chatbot collects two required inputs:
   - Target language
   - Text to translate
4. Amazon Lex invokes the associated AWS Lambda function.
5. Lambda extracts the user's input and target language.
6. Lambda calls Amazon Translate to perform the translation.
7. Amazon Translate returns the translated text.
8. Lambda sends the translated result back to Amazon Lex.
9. Amazon Lex displays the translated text to the user.

## Supported Languages

The chatbot supports the following target languages:

| Language | Language Code |
|---|---|
| French | fr |
| Japanese | ja |
| Chinese | zh |
| Spanish | es |
| German | de |
| Norwegian | no |

Amazon Translate automatically detects the source language using the `auto` source language setting.

## Implementation Steps

### 1. Create an Amazon Lex Bot

1. Navigate to Amazon Lex in the AWS Management Console.
2. Select **Create Bot**.
3. Choose the Traditional creation method.
4. Create a blank bot without preconfigured languages, intents, or slot types.
5. Configure the bot:
   - Bot Name: `TextTranslatorBryan`
   - Runtime Role: Create a role with basic Amazon Lex permissions.
   - COPPA: No
   - Bot Error Logging: Disabled
6. Add English (US) as the primary language.

**Note:** Amazon Lex is not available in every AWS Region. Select a supported Region, such as Asia Pacific (Singapore), before creating the bot.

### 2. Configure the Translation Intent

Create a new intent named:

`TranslationIntent`

Add the following sample utterances:

- I want to translate
- Can you help me translate
- Translate for me
- I want to translate in {language}
- Translate in {language}
- Can you translate in {language} for me

### 3. Configure Slot Types and Slots

Create a custom slot type named `language` and add the following values:

- French
- Japanese
- Chinese
- Spanish
- German
- Norwegian

Add the following slots to `TranslationIntent`:

| Slot Name | Slot Type | Prompt |
|---|---|---|
| language | language (Custom Slot Type) | In which language would you like to translate the text? |
| text | AMAZON.FreeFormInput | Please input the text you want to translate. |

Configure the initial response:

"Sure, I can help you with that!"

Configure the fulfilment responses:

- Successful fulfilment: Your request was completed successfully.
- Failed fulfilment: Something went wrong.

Enable **Use a Lambda function for fulfilment** under Advanced Options.

Configure the closing response:

"I hope the translation was helpful."

### 4. Create an IAM Role for Lambda

Navigate to IAM and create a new role.

Configuration:

- Trusted Entity: AWS Service
- Use Case: Lambda

Attach the following permission policies:

- `TranslateFullAccess`
- `AWSLambdaBasicExecutionRole`

Role Name:

`LambdaBotRole`

**Security Note:** For a production environment, replace broad TranslateFullAccess permissions with a custom least-privilege IAM policy granting only the required Amazon Translate permissions.

### 5. Create the AWS Lambda Function

Navigate to AWS Lambda and create a function.

Configuration:

- Creation Method: Author from Scratch
- Function Name: `TranslateBot`
- Runtime: Python
- Architecture: Default
- Execution Role: Use an existing role
- Existing Role: `LambdaBotRole`

Replace the default Lambda code with the Python implementation provided in the `docs` folder.

**Source Code:** [docs/lambda_function.py](docs/lambda_function.py)

Select **Deploy** after adding the code.

### 6. Test the Lambda Function

Create a test event named:

`translationtest`

Use the test event JSON provided in:

[docs/test-event.json](docs/test-event.json)

The test event contains sample input text and the target language required to simulate an Amazon Lex request.

Select **Test** to execute the Lambda function.

Expected translated output:

`Bonjour`

### 7. Connect Lambda to Amazon Lex

Return to the Amazon Lex console.

1. Open the `TextTranslatorBryan` bot.
2. Navigate to the `TranslationIntent`.
3. Open the fulfilment settings.
4. Enable the Lambda function integration.
5. Select `TranslateBot` as the Lambda function.
6. Save the configuration.
7. Build the bot.

Ensure the Lambda function is associated with the correct bot alias and language configuration, and that Lex has permission to invoke it.

### 8. Test the Chatbot

Open the Amazon Lex testing console and test the chatbot using different languages.

Example interaction:

**User:** I want to translate in French.

**Chatbot:** In which language would you like to translate the text?

**User:** French.

**Chatbot:** Please input the text you want to translate.

**User:** Hello.

**Chatbot:** Bonjour.

## Testing Results

The chatbot successfully processes translation requests and returns translated text for different target languages.

Example test cases:

| Input Text | Target Language | Expected Translation |
|---|---|---|
| Hello | French | Bonjour |
| Hello | Japanese | こんにちは |
| Hello | Spanish | Hola |
| Hello | German | Hallo |

## Key Learning Outcomes

Through this project, I gained practical experience in:

- Building conversational chatbots using Amazon Lex.
- Configuring intents, sample utterances, custom slot types, and slot prompts.
- Integrating AWS Lambda with Amazon Lex fulfilment.
- Using Amazon Translate API through the AWS SDK for Python (Boto3).
- Implementing automatic source language detection
