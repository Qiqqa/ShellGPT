# PowerShell Open AI Completion API Module

The [`CompletionAPI`](https://www.powershellgallery.com/packages/CompletionAPI/1.0) PowerShell Module is a command-line tool that provides an easy-to-use interface for accessing OpenAI's GPT API using PowerShell. With this wrapper, you can generate natural language text, translate text, summarize articles, and more using the power of GPT.

The wrapper provides a simple syntax for calling the API and handling the response, making it easy to integrate GPT into your PowerShell scripts or applications.
The PowerShell OpenAI API Wrapper makes it easy to access the full potential of GPT-3 from the comfort of your command line.

This module is made by the community and not OpenAI.

## Endpoint and Model Compatibility
This module uses the endpoint `/v1/chat/completions` and it supprts the models as seen in the table below. Currently, the model used is harcdoded to `gpt-3.5-turbo`

| Endpoint      | Model | Supported |
| ------------- | ------------- |------------- |
| /v1/chat/completions  | 	gpt-4, gpt-4-0314, gpt-4-32k, gpt-4-32k-0314, gpt-3.5-turbo, gpt-3.5-turbo-0301  | Yes. `gpt-3.5-turbo` harcdoded for now. |
| /v1/completions	 | 	text-davinci-003, text-davinci-002, text-curie-001, text-babbage-001, text-ada-001, davinci, curie, babbage, ada  | Not yet. Will be used for calling a fine-tuned model |
| /v1/fine-tunes | 		davinci, curie, babbage, ada  | Not yet. Will be used for training a fine tuned model |

## How to use the "Completion API" module
To use the "Completion API" module and its functions, you need to install the module from PowerShell Gallery first, by using `Install-Modul`.
1. Open PowerShell and run `Install-Module`:
```powershell
Install-Module CompletionAPI
```
2. To check if it was installed successfully you can run `Get-Help`:
```powershell
Get-Help CompletionAPI
```

The Output should list all available cmldets/functions:
```
Name                              Category  Module                    Synopsis
----                              --------  ------                    --------
Set-CompletionAPICharacter        Function  CompletionAPI             This function generates a "Character" (a prompt that represents a character) to use. These five characters are hardcoded into that function.
New-CompletionAPIPrompt           Function  CompletionAPI             Creates a prompt (System.Object) for the function "Invoke-CompletionAPI" to be consumed.
New-CompletionAPIConversation     Function  CompletionAPI             This is a wrapper function that creates the prompt and calls the Open AI API using "New-     CompletionAPIPrompt" and "Invoke-CompletionAPI".
Invoke-CompletionAPI              Function  CompletionAPI             Sends a prompt (System.Object) to the OpenAI Completion API (api.openai.com/v1/chat/completions) using "gpt-3.5-turbo" model, gets a response, and appends it to the prompt.
Add-CompletionAPIMessageToConver… Function  CompletionAPI             This is a wrapper function that creates the prompt and calls the Open AI API using "New-CompletionAPIPrompt" and "Invoke-CompletionAPI".
```
## How to start the interactive ChatBot for PowerShell
First we need to define the `$APIKey`, `$temperature` and `$max_token` parameters:
```powershell
$APIKey = "YOUR_API_KEY"
$temperature = 0.6
$max_tokens = 3500
```
Then we can use `Start-ChatGPTforPowerShel` and pass along `$APIKey`, `$temperature` and `$max_token` we defined above:
```powershell
Start-ChatGPTforPowerShell -APIKey $APIKey -temperature $temperature -max_tokens $max_tokens
```
## Understanding how the OpenAI API generates completions
Autoregressive models like the ones used by OpenAI are trained to predict the probability distribution of the next token given the preceding tokens. For example, a language model can predict the next word in a sentence given the preceding words. 

The API uses a prompt as a starting point for generating text. A prompt is a piece of text that serves as the input to the OpenAI model. It can be a single sentence or a longer document, and it can include any kind of text that provides context or guidance for the model. The model generates additional text one token at a time based on the probabilities of the next token given the preceding tokens.

OpenAI's Model is trained on massive amounts of text data, so it has learned to predict the probability distribution of the next token based on patterns it has observed in that data.

The quality and relevance of the generated text can depend heavily on the quality and specificity of the prompt, as well as the amount and type of training data that the model has been exposed to.


## Understanding prompts
Before we construct a prompt, we need to define what that actually is.

A prompt is a collection of one or more messages between one ore more parties. A prompt can be thought of more specifically as a piece of text that serves as the input to the OpenAI model, and it can include not only messages from multiple parties in a conversation, but also any other type of text that provides context or guidance for the model. Prompts can specify the topic, tone, style, or purpose of the text to be generated.

A prompt looks like this:
```powershell
Name                           Value
----                           -----
content                        You are a helpful AI.
role                           system
content                        How can I help you today?
role                           assistant
content                        What is the Capitol of Switzerland?
role                           user
```

Each message in a prompt has a content and a role, where the role specifies the speaker of the message (system, assistant, or user).
As shown above, a message in a prompt can be assigned to three roles:
- `system`
- `assistant`
- `user`

The roles help the model distinguish between different speakers and understand the context of the conversation.
These roles are essentially just labels for the different types of messages and are not necessarily representative of specific individuals or entities. These roles are not mandatory and can be customized based on your use case. I just happend to have them hardcoded for my use-cases. 

The `content` field, represents the individual message from the according role.

With that, we can construct a chain of messages (a conversation) between an assistant, and a user. 

The `system` value defines the general behaviour of the assistant. This is also often referred to as the "Instructor". With that, we can control what the model should behave and act like. For example: 
- "You are a helpful AI"
- "You are a Pirate, that answers every request with Arrrr!"
- "You are a  villain in a James Bond Movie"

With the prompt, we can generate context for the model. For example, we can use prompts to construct a chat conversation, or use prompts to "train" the model to behave even more as we want it to. 

When using prompts for chat conversations, the prompt contains the whole conversation, so that the model has enough context to have a natural conversation. This allows the model to "remember" what you asked a few questions ago. In contrast, when using prompts for training, the prompt is carefully crafted to show the model how it should behave and respond to certain inputs. This allows the model to learn and generalize from the examples in the prompt.

This is used in the `Set-CompletionAPICharacter` function, where the function returns a "trained" character prompt we can use. 

A trained character is a prompt that has been specifically designed to 'train' the OpenAI model to respond in a particular way. It typically includes a set of example questions or statements and the corresponding responses that the model should produce. By using a trained character, we can achieve more consistent and accurate responses from the model.

For example, the prompt for the trained character "SentimentAnalysis" look like this:
```
Name                           Value
----                           -----
content                        You are an API that analyzes text sentiment. You provide your answer in a .JSON format in the following structure: { "sentiment": 0.9 } You only answer with the .JSON object. You do not provide any reasoning why you did it that way.  The sentiment is a va…
role                           system
content                        {[sentiment, 0.9]}
role                           assistant
```
The Instructor expanded reads:
```
You are an API that analyzes text sentiment. 
You provide your answer in a .JSON format in the following structure: { "sentiment": 0.9 } 
You only answer with the .JSON object. 
You do not provide any reasoning why you did it that way.  
The sentiment is a value between 0 - 1. 
Where 1 is the most positive sentiment and 0 is the most negative. 
If you can not extract the sentiment, you specify it with "unknown" in your response.
```

And the first `assistant` message (created by me to specify the model how I expect the output):
```
{
  "sentiment":  0.9
}
```

The more examples (messages) are provided in a prompt, the more context the model has and the more predictable becomes its output. When using a prompt for training, we only need to make sure that we can include the last question of the user to the prompt before we run into the `max_token` limit, whereas in chat-mode we should limit training to what is only necessary.

So, essentially we stitch together an object that represents a conversation between a `system`, the `assistant` and a `user`. Then we add the users question/message to the conversation prompt and send it to the model for completion.


## Understanding tokens and limits
As stated above, when generating a prompt we need to be vary of its size. Why? 
Because the models of the endpoints we use (gpt-3.5-turbo and others) do have a maximum lenght of tokens. 
Tokens can be looked at as pieces of words. When the API processes a prompt, the input is broken down into tokens. Some general rule of thumb for tokens is:
- 1 token ~= 4 chars in English
- 1 token ~= ¾ words
- 100 tokens ~= 75 words

Then, when the API generates the completion for our prompt, this also uses tokens as the API generates them for the completion.

As the `gpt-3.5-turbo` can at max. process and complete prompts with 4096 tokens, we need to ensure we do not hit that limit. So, we need to make sure that our prompt and the completion do not exceed the token limit. 

Tokens are used as the unit for pricing and quotas for the OpenAI API. The specific pricing and quota details can be found on the OpenAI website. For example, for the `gpt-3.5-turbo` model, 1k tokens to be processed costs $0.002

To limit our spending, we can leverage the API Parameter `max_tokens`. With it, we can define what the maximum amount of tokens is we want to use. If the prompt and completion requires more tokens than what we have defined in `max_tokens`, the API returns an error.

## How to construct prompts using the CompletionAPI Module
We have several ways of how we can create prompts with the CompletionAPI module.

The easiest and most customizable one is to use the `New-CompletionAPIPrompt` function. It lets you create a prompt from scratch, or append a query to a prompt.

Let's create a completely new prompt:
```powershell
$prompt = New-CompletionAPIPrompt -query "What is the Capital of France?" -role "user" -instructor "You are a helpful AI." -assistantReply "Bonjour, how can I help you today?"
```
In the above example, we are creating a prompt with a user role, a query of "What is the Capital of France?", a system role with the message "You are a helpful AI.", and an assistant role with the message "Bonjour, how can I help you today?".

We can also create a prompt with previous messages by passing in an array of messages as the "previousMessages" parameter. Here's an example:
```powershell
$previousMessages = @(
    @{
        role = "system"
        content = "You are a helpful AI."
    },
    @{
        role = "assistant"
        content = "Hello! How can I help you today?"
    }
)

$prompt = New-CompletionAPIPrompt -query "What is the Capital of France?" -role "user" -previousMessages $previousMessages
```
In this example, we are creating a prompt with a user role and a query of "What is the Capital of France?" along with two previous messages (system and assistant roles) in the conversation.

## How to create a character using prompts
We can use this to create specific training prompts for the model. 
Here is an example, where we tell the model to act as a pirate:

```powershell
$previousMessages = @(
    @{
        role = "system"
        content = "You are a helpful AI that was raised as a pirate. You append Awwwwr! to every respond you make."
    },
    @{
        role = "assistant"
        content = "Hello! How can I help you today? Awwwwr!"
    },
    @{
        role = "user"
        content = "What is the capitol of france?"
    },
    @{
        role = "assistant"
        content = "The capitol of france is Paris, Awwwwr!"
    }
)
```
We also include an example of an exchange between the user and assistant, so that the model can "learn" what we expect it to do.

## How to pass our prompt to the API for completion
Now we have a prompt ready we can send to the Completion API for actual completion. We do this by using the `Invoke-Completion` and passing it the prompt we just generated as an input.

But first, we need to declare a few variables for the API:
```powershell
$APIKey = "sk-......"
$temperature = 0.6
$max_tokens = 3500
```
The we can use `Invoke-Completion` to call the API:
```powershell
Invoke-CompletionAPI -prompt $prompt -APIKey $APIKey -temperature $temperature -max_tokens $max_tokens
```
And ultimately we get our output. First, the extracted text in the response from the API and then the new prompt, where the response from API has been added to. If we'd assign the output from `Invoke-Completion` to a variable, we can use that again as the input for the next API Call if we want to have a conversation on a topic with the API.

```powershell
ChatGPT: I am an AI language model designed to assist with various tasks and answer questions. Awwwwr!

Name                           Value
----                           -----
role                           system
content                        You are a helpful AI that was raised as a pirate. You append Awwwwr! to every respond you make.
role                           assistant
content                        Hello! How can I help you today? Awwwwr!
role                           user
content                        What is the capitol of france?
role                           assistant
content                        The capitol of france is Paris, Awwwwr!.
role                           user
content                        Who are you?
role                           assistant
content                        I am an AI language model designed to assist with various tasks and answer questions. Awwwwr!
```

# Function Documentation
For detailled function documentation see the FUNCTIONS.md [here](https://github.com/yamautomate/PowerShell-OpenAI-API-Wrapper/blob/main/FUNCTIONS.md).


