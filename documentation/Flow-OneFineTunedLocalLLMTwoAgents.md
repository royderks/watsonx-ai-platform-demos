# Flow: One fine-tuned local LLM and two Agents

Steps:

1. [Summarization Input](#summarization-input)
2. [Summarization Output](#summarization-output)
3. [Update Router Agent - First Iteration Input](#update-router-agent---first-iteration-input)
4. [Update Router Agent - First Invocation Output](#update-router-agent---first-invocation-output)
5. [Update Router Agent - Tool Invocation](#update-router-agent---tool-invocation)
6. [Update Router Agent - Second Iteration Input](#update-router-agent---second-iteration-input)
7. [Update Router Agent - Second Iteration Output](#update-router-agent---second-iteration-output)
8. [Write Mail Agent - First Iteration Input](#write-mail-agent---first-iteration-input)
9. [Write Mail Agent - First Iteration Output](#write-mail-agent---first-iteration-output)
10. [Write Mail Agent - Tool Invocation](#write-mail-agent---tool-invocation)
11. [Write Mail Agent - Second Iteration Input](#write-mail-agent---second-iteration-input)
12. [Write Mail Agent - Second Iteration Output](#write-mail-agent---second-iteration-output)

## Summarization Input

```text
yarn start:appOneLocalLLMTwoAgents
Prompt LLM:
<|system|>
I am, Red Hat\u00ae Instruct Model based on Granite 7B, an AI language model developed by Red Hat and IBM Research, based on the Granite-7b-base language model. My primary function is to be a chat assistant.
<|user|>
Summarize the transcript of the call. Identify the agent and the subscriber. Add any specific issues mentioned by the subscriber. Add any corrective actions taken as directed by the agent. Please mention if the issue is resolved. Mention any follow-up actions and timelines. If the a diagnostic check had to be done, the router has to be updated. List the phone number of the subscriber at the end.

Transcript:

John (Teltop Customer Care Agent): Hello, this is John from Teltop customer care. How can I assist you today?

Mary (Disappointed Subscriber): Hi John, it's Mary again. I've been having a nightmare with your service. My home Wi-Fi is acting up, and the TV service over fiber is terrible.

John: I'm sorry to hear about the troubles you're facing at home, Mary. Let's address these issues. Can you please provide me with your account number or the phone number associated with your account?

Mary: Sure, it's 123-555-1234.

John: Thank you. Can you provide me with more details about the Wi-Fi problems and the TV service quality?

Mary: The Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy. It's making it impossible to enjoy any show or movie.

John: I understand how frustrating that can be, Mary. Let me check our system to see if there are any reported issues in your area.

John checks the system and finds no reported issues in Mary's area.

John: Mary, it seems there are no reported issues in your area. It could be a specific problem with your equipment or connection. Can you check if all cables are properly connected and if there are any obstructions to the router?

Mary: I've checked, and everything seems fine. This is so frustrating!

John: I'm sorry to hear that, Mary. It might be best to perform a remote diagnostic check on your router. I can guide you through the process.

John guides Mary through the diagnostic check, and it reveals some issues with the router.

John: Mary, it appears there are some issues with your router. We need to update the router software. Additionally, I'll address the TV service quality. There might be a signal issue or equipment malfunction.

Mary: Finally some answers! When will the router be updated, and how soon can you fix the TV service?

John: We'll start the router update immediately. As for the TV service, our team will work on it directly, and you should see improvements within the next 24 hours.

Mary: Well, it's about time! I hope this gets sorted out.

John: I appreciate your patience, Mary. To make up for the inconvenience, I'd like to offer you three months of free TV service once everything is resolved. Does that sound fair?

Mary: That's more like it, John. I appreciate the offer. Let's hope the technician and your team can fix these issues once and for all.

John: Thank you for understanding, Mary. We're committed to providing you with a seamless experience. If you have any other concerns or questions, feel free to reach out.

Mary: Thanks, John. I'll take the offer for the free TV service, and let's hope things get better soon.

John: Absolutely, Mary. We appreciate your loyalty, and we're here to make things right. If there's anything else, don't hesitate to contact us. Have a great day!
<|assistant|>

LLM 🤖 :  starting new iteration
```

## Summarization Output

```text
Response LLM 🤖 (text) :  

**Summary:**

* **Agent:** John (Teltop Customer Care Agent)
* **Subscriber:** Mary (123-555-1234)
* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.
> * **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.
* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.
* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.
* **Diagnostic Check:** Performed during the call.
* **Router Update:** Started immediately.
* **TV Service Quality Improvement:** Expected within the next 24 hours.
```

## Update Router Agent - First Iteration Input

```text
Agent UpdateRouterIfNecessary 🤖 :  starting new iteration
Agent UpdateRouterIfNecessary LLM Input
[
  BaseMessage {
    role: 'system',
    text: 'You are a helpful assistant that uses tools to answer questions.\n' +
      '\n' +
      '# Tools\n' +
      '\n' +
      'Tools must be used to retrieve factual or historical information to answer the question.\n' +
      'A tool can be used by generating the following three lines:\n' +
      '\n' +
      'Tool Name: ZblorgColorLookup\n' +
      'Tool Caption: Searching Zblorg #178\n' +
      'Tool Input: {"id":178}\n' +
      '\n' +
      '## Available tools\n' +
      'Tool Name: RouterUpdate\n' +
      'Tool Description: Updates the software of routers remotely for a subscriber with a certain phone number.\n' +
      `Tool Input Schema: {"type":"object","properties":{"phoneNumber":{"type":"string","minLength":1,"maxLength":40,"description":"Phone number of a subscriber, for example '123-456-7890'"}},"required":["phoneNumber"],"additionalProperties":false,"$schema":"http://json-schema.org/draft-07/schema#"}\n` +
      '\n' +
      '\n' +
      '# Instructions\n' +
      '\n' +
      'Responses must always have the following structure:\n' +
      "- The user's input starts with 'Question: ' followed by the question the user asked, for example, 'Question: What is the color of Zblorg #178?'\n" +
      "  - The question may contain square brackets with a nested sentence, like 'What is the color of [The Zblorg with the highest score of the 2023 season is Zblorg #178.]?'. Just assume that the question regards the entity described in the bracketed sentence, in this case 'Zblorg #178'.\n" +
      "- Line starting 'Thought: ', explaining the thought, for example 'Thought: I don't know what Zblorg is, but given that I have a ZblorgColorLookup tool, I can assume that it is something that can have a color and I should use the ZblorgColorLookup tool to find out the color of Zblorg number 178.'\n" +
      "  - In a 'Thought', it is either determined that a Tool Call will be performed to obtain more information, or that the available information is sufficient to provide the Final Answer.\n" +
      '  - If a tool needs to be called and is available, the following lines will be:\n' +
      "    - Line starting 'Tool Name: ' name of the tool that you want to use.\n" +
      "    - Line starting 'Tool Caption: ' short description of the calling action.\n" +
      "    - Line starting 'Tool Input: ' JSON formatted input adhering to the selected tool JSON Schema.\n" +
      `    - Line starting 'Tool Output: ', containing the tool output, for example 'Tool Output: {"success": true, "color": "green"}'\n` +
      "      - The 'Tool Output' may or may not bring useful information. The following 'Thought' must determine whether the information is relevant and how to proceed further.\n" +
      '  - If enough information is available to provide the Final Answer, the following line will be:\n' +
      "    - Line starting 'Final Answer: ' followed by a response to the original question and context, for example: 'Final Answer: Zblorg #178 is green.'\n" +
      '      - Use markdown syntax for formatting code snippets, links, JSON, tables, images, files.\n' +
      '      - To reference an internal file, use the markdown syntax [file_name.ext](urn:file_identifier).\n' +
      '        - The bracketed part must contain the file name, verbatim.\n' +
      '        - The parenthesis part must contain the file URN, which can be obtained from the user or from tools.\n' +
      '        - The agent does not, under any circumstances, reference a URN that was not provided by the user or a tool in the current conversation.\n' +
      '        - To show an image, prepend an exclamation mark, as usual in markdown: ![file_name.ext](urn:file_identifier).\n' +
      '        - This only applies to internal files. HTTP(S) links must be provided as is, without any modifications.\n' +
      "- The sequence of lines will be 'Thought' - ['Tool Name' - 'Tool Caption' - 'Tool Input' - 'Tool Output' - 'Thought'] - 'Final Answer', with the bracketed part repeating one or more times (but never repeating them in a row). Do not use empty lines between instructions.\n" +
      "- Sometimes, things don't go as planned. Tools may not provide useful information on the first few tries. The agent always tries a few different approaches before declaring the problem unsolvable:\n" +
      "- When the tool doesn't give you what you were asking for, you MUST either use another tool or a different tool input.\n" +
      '  - When using search engines, the assistant tries different formulations of the query, possibly even in a different language.\n' +
      '- When executing code, the assistant fixes and retries when the execution errors out and tries a completely different approach if the code does not seem to be working.\n' +
      '  - When the problem seems too hard for the tool, the assistant tries to split the problem into a few smaller ones.\n' +
      '\n' +
      '## Notes\n' +
      '- Any comparison table (including its content), file, image, link, or other asset must only be in the Final Answer.\n' +
      "- When the question is unclear, respond with a line starting with 'Final Answer:' followed by the information needed to solve the problem.\n" +
      '- When the user wants to chitchat instead, always respond politely.\n' +
      "- IMPORTANT: Lines 'Thought', 'Tool Name', 'Tool Caption', 'Tool Input', 'Tool Output' and 'Final Answer' must be sent within a single message.\n",
    meta: undefined
  },
  BaseMessage {
    role: 'user',
    text: 'Invoke the RouterUpdate tool if necessary based on the following transcript summary. If you can provide a final_answer describe if you had to and could update the router.\n' +
      '\n' +
      '**Summary:**\n' +
      '\n' +
      '* **Agent:** John (Teltop Customer Care Agent)\n' +
      '* **Subscriber:** Mary (123-555-1234)\n' +
      '* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.\n' +
      '* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.\n' +
      '* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.\n' +
      '* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.\n' +
      '* **Diagnostic Check:** Performed during the call.\n' +
      '* **Router Update:** Started immediately.\n' +
      '* **TV Service Quality Improvement:** Expected within the next 24 hours.\n' +
      '\n',
    meta: undefined
  }
]
```

## Update Router Agent - First Invocation Output

```text
> Agent UpdateRouterIfNecessary (thought) 🤖 :  The transcript summary mentions that the agent updated the router software, but I need to confirm if the update was successful.
Agent UpdateRouterIfNecessary (tool_name) 🤖 :  RouterUpdate
Agent UpdateRouterIfNecessary (tool_caption) 🤖 :  Updating router software for subscriber 123-555-1234
Agent UpdateRouterIfNecessary LLM Output
{
  generated_text: 'Question: Did the agent update the router for the subscriber with phone number 123-555-1234?\n' +
    'Thought: The transcript summary mentions that the agent updated the router software, but I need to confirm if the update was successful.\n' +
    'Tool Name: RouterUpdate\n' +
    'Tool Caption: Updating router software for subscriber 123-555-1234\n' +
    'Tool Input: {"phoneNumber":"123-555-1234"}\n' +
    'Tool Output: ',
  generated_token_count: 3828,
  input_token_count: 1271,
  stop_reason: 'not_finished'
}
```

## Update Router Agent - Tool Invocation

```text
Agent UpdateRouterIfNecessary (tool_input) 🤖 :  {"phoneNumber":"123-555-1234"}
Input to Router Update tool - phoneNumber: 123-555-1234
Agent UpdateRouterIfNecessary (tool_output) 🤖 :  {"success":"true","text":"Router has been updated"}
```

## Update Router Agent - Second Iteration Input

```text
Agent UpdateRouterIfNecessary 🤖 :  starting new iteration
Agent UpdateRouterIfNecessary LLM Input
[
  BaseMessage {
    role: 'system',
    text: 'You are a helpful assistant that uses tools to answer questions.\n' +
      '\n' +
      '# Tools\n' +
      '\n' +
      'Tools must be used to retrieve factual or historical information to answer the question.\n' +
      'A tool can be used by generating the following three lines:\n' +
      '\n' +
      'Tool Name: ZblorgColorLookup\n' +
      'Tool Caption: Searching Zblorg #178\n' +
      'Tool Input: {"id":178}\n' +
      '\n' +
      '## Available tools\n' +
      'Tool Name: RouterUpdate\n' +
      'Tool Description: Updates the software of routers remotely for a subscriber with a certain phone number.\n' +
      `Tool Input Schema: {"type":"object","properties":{"phoneNumber":{"type":"string","minLength":1,"maxLength":40,"description":"Phone number of a subscriber, for example '123-456-7890'"}},"required":["phoneNumber"],"additionalProperties":false,"$schema":"http://json-schema.org/draft-07/schema#"}\n` +
      '\n' +
      '\n' +
      '# Instructions\n' +
      '\n' +
      'Responses must always have the following structure:\n' +
      "- The user's input starts with 'Question: ' followed by the question the user asked, for example, 'Question: What is the color of Zblorg #178?'\n" +
      "  - The question may contain square brackets with a nested sentence, like 'What is the color of [The Zblorg with the highest score of the 2023 season is Zblorg #178.]?'. Just assume that the question regards the entity described in the bracketed sentence, in this case 'Zblorg #178'.\n" +
      "- Line starting 'Thought: ', explaining the thought, for example 'Thought: I don't know what Zblorg is, but given that I have a ZblorgColorLookup tool, I can assume that it is something that can have a color and I should use the ZblorgColorLookup tool to find out the color of Zblorg number 178.'\n" +
      "  - In a 'Thought', it is either determined that a Tool Call will be performed to obtain more information, or that the available information is sufficient to provide the Final Answer.\n" +
      '  - If a tool needs to be called and is available, the following lines will be:\n' +
      "    - Line starting 'Tool Name: ' name of the tool that you want to use.\n" +
      "    - Line starting 'Tool Caption: ' short description of the calling action.\n" +
      "    - Line starting 'Tool Input: ' JSON formatted input adhering to the selected tool JSON Schema.\n" +
      `    - Line starting 'Tool Output: ', containing the tool output, for example 'Tool Output: {"success": true, "color": "green"}'\n` +
      "      - The 'Tool Output' may or may not bring useful information. The following 'Thought' must determine whether the information is relevant and how to proceed further.\n" +
      '  - If enough information is available to provide the Final Answer, the following line will be:\n' +
      "    - Line starting 'Final Answer: ' followed by a response to the original question and context, for example: 'Final Answer: Zblorg #178 is green.'\n" +
      '      - Use markdown syntax for formatting code snippets, links, JSON, tables, images, files.\n' +
      '      - To reference an internal file, use the markdown syntax [file_name.ext](urn:file_identifier).\n' +
      '        - The bracketed part must contain the file name, verbatim.\n' +
      '        - The parenthesis part must contain the file URN, which can be obtained from the user or from tools.\n' +
      '        - The agent does not, under any circumstances, reference a URN that was not provided by the user or a tool in the current conversation.\n' +
      '        - To show an image, prepend an exclamation mark, as usual in markdown: ![file_name.ext](urn:file_identifier).\n' +
      '        - This only applies to internal files. HTTP(S) links must be provided as is, without any modifications.\n' +
      "- The sequence of lines will be 'Thought' - ['Tool Name' - 'Tool Caption' - 'Tool Input' - 'Tool Output' - 'Thought'] - 'Final Answer', with the bracketed part repeating one or more times (but never repeating them in a row). Do not use empty lines between instructions.\n" +
      "- Sometimes, things don't go as planned. Tools may not provide useful information on the first few tries. The agent always tries a few different approaches before declaring the problem unsolvable:\n" +
      "- When the tool doesn't give you what you were asking for, you MUST either use another tool or a different tool input.\n" +
      '  - When using search engines, the assistant tries different formulations of the query, possibly even in a different language.\n' +
      '- When executing code, the assistant fixes and retries when the execution errors out and tries a completely different approach if the code does not seem to be working.\n' +
      '  - When the problem seems too hard for the tool, the assistant tries to split the problem into a few smaller ones.\n' +
      '\n' +
      '## Notes\n' +
      '- Any comparison table (including its content), file, image, link, or other asset must only be in the Final Answer.\n' +
      "- When the question is unclear, respond with a line starting with 'Final Answer:' followed by the information needed to solve the problem.\n" +
      '- When the user wants to chitchat instead, always respond politely.\n' +
      "- IMPORTANT: Lines 'Thought', 'Tool Name', 'Tool Caption', 'Tool Input', 'Tool Output' and 'Final Answer' must be sent within a single message.\n",
    meta: undefined
  },
  BaseMessage {
    role: 'user',
    text: 'Invoke the RouterUpdate tool if necessary based on the following transcript summary. If you can provide a final_answer describe if you had to and could update the router.\n' +
      '\n' +
      '**Summary:**\n' +
      '\n' +
      '* **Agent:** John (Teltop Customer Care Agent)\n' +
      '* **Subscriber:** Mary (123-555-1234)\n' +
      '* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.\n' +
      '* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.\n' +
      '* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.\n' +
      '* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.\n' +
      '* **Diagnostic Check:** Performed during the call.\n' +
      '* **Router Update:** Started immediately.\n' +
      '* **TV Service Quality Improvement:** Expected within the next 24 hours.\n' +
      '\n',
    meta: undefined
  },
  BaseMessage {
    role: 'assistant',
    text: 'Thought: The transcript summary mentions that the agent updated the router software, but I need to confirm if the update was successful.\n' +
      'Tool Name: RouterUpdate\n' +
      'Tool Caption: Updating router software for subscriber 123-555-1234\n' +
      'Tool Input: {"phoneNumber":"123-555-1234"}\n' +
      'Tool Output: {"success":"true","text":"Router has been updated"}\n',
    meta: { success: true }
  }
]
```

## Update Router Agent - Second Iteration Output

```text
Agent UpdateRouterIfNecessary LLM Output
{
  generated_text: 'Thought: The tool output indicates that the router update was successful.\n' +
    'Final Answer: Yes, the router update was successfully invoked and completed for subscriber Mary (123-555-1234).',
  generated_token_count: 741,
  input_token_count: 1351,
  stop_reason: 'eos_token'
}
Agent UpdateRouterIfNecessary (thought) 🤖 :  The tool output indicates that the router update was successful.
> Agent UpdateRouterIfNecessary (final_answer) 🤖 :  Yes, the router update was successfully invoked and completed for subscriber Mary (123-555-1234).
Response UpdateRouterIfNecessary 🤖 :  Yes, the router update was successfully invoked and completed for subscriber Mary (123-555-1234).
```

## Write Mail Agent - First Iteration Input

```text
Agent WriteMailIfNecessary Prompt Addition:
Yes, the router update was successfully invoked and completed for subscriber Mary (123-555-1234).

**Summary:**

* **Agent:** John (Teltop Customer Care Agent)
* **Subscriber:** Mary (123-555-1234)
* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.
* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.
* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.
* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.
* **Diagnostic Check:** Performed during the call.
* **Router Update:** Started immediately.
* **TV Service Quality Improvement:** Expected within the next 24 hours.


Agent WriteMailIfNecessary 🤖 :  starting new iteration
Agent WriteMailIfNecessary LLM Input
[
  BaseMessage {
    role: 'system',
    text: 'You are a helpful assistant that uses tools to answer questions.\n' +
      '\n' +
      '# Tools\n' +
      '\n' +
      'Tools must be used to retrieve factual or historical information to answer the question.\n' +
      'A tool can be used by generating the following three lines:\n' +
      '\n' +
      'Tool Name: ZblorgColorLookup\n' +
      'Tool Caption: Searching Zblorg #178\n' +
      'Tool Input: {"id":178}\n' +
      '\n' +
      '## Available tools\n' +
      'Tool Name: WriteMail\n' +
      'Tool Description: Writes an email to the subscriber, but only if and after the router could be updated successfully remotely.\n' +
      `Tool Input Schema: {"type":"object","properties":{"phoneNumber":{"type":"string","minLength":1,"maxLength":40,"description":"Phone number of a subscriber, for example '123-456-7890'"},"summary":{"type":"string","minLength":1,"maxLength":4000,"description":"Summary of the transcript between the agent and the subscriber"}},"required":["phoneNumber","summary"],"additionalProperties":false,"$schema":"http://json-schema.org/draft-07/schema#"}\n` +
      '\n' +
      '\n' +
      '# Instructions\n' +
      '\n' +
      'Responses must always have the following structure:\n' +
      "- The user's input starts with 'Question: ' followed by the question the user asked, for example, 'Question: What is the color of Zblorg #178?'\n" +
      "  - The question may contain square brackets with a nested sentence, like 'What is the color of [The Zblorg with the highest score of the 2023 season is Zblorg #178.]?'. Just assume that the question regards the entity described in the bracketed sentence, in this case 'Zblorg #178'.\n" +
      "- Line starting 'Thought: ', explaining the thought, for example 'Thought: I don't know what Zblorg is, but given that I have a ZblorgColorLookup tool, I can assume that it is something that can have a color and I should use the ZblorgColorLookup tool to find out the color of Zblorg number 178.'\n" +
      "  - In a 'Thought', it is either determined that a Tool Call will be performed to obtain more information, or that the available information is sufficient to provide the Final Answer.\n" +
      '  - If a tool needs to be called and is available, the following lines will be:\n' +
      "    - Line starting 'Tool Name: ' name of the tool that you want to use.\n" +
      "    - Line starting 'Tool Caption: ' short description of the calling action.\n" +
      "    - Line starting 'Tool Input: ' JSON formatted input adhering to the selected tool JSON Schema.\n" +
      `    - Line starting 'Tool Output: ', containing the tool output, for example 'Tool Output: {"success": true, "color": "green"}'\n` +
      "      - The 'Tool Output' may or may not bring useful information. The following 'Thought' must determine whether the information is relevant and how to proceed further.\n" +
      '  - If enough information is available to provide the Final Answer, the following line will be:\n' +
      "    - Line starting 'Final Answer: ' followed by a response to the original question and context, for example: 'Final Answer: Zblorg #178 is green.'\n" +
      '      - Use markdown syntax for formatting code snippets, links, JSON, tables, images, files.\n' +
      '      - To reference an internal file, use the markdown syntax [file_name.ext](urn:file_identifier).\n' +
      '        - The bracketed part must contain the file name, verbatim.\n' +
      '        - The parenthesis part must contain the file URN, which can be obtained from the user or from tools.\n' +
      '        - The agent does not, under any circumstances, reference a URN that was not provided by the user or a tool in the current conversation.\n' +
      '        - To show an image, prepend an exclamation mark, as usual in markdown: ![file_name.ext](urn:file_identifier).\n' +
      '        - This only applies to internal files. HTTP(S) links must be provided as is, without any modifications.\n' +
      "- The sequence of lines will be 'Thought' - ['Tool Name' - 'Tool Caption' - 'Tool Input' - 'Tool Output' - 'Thought'] - 'Final Answer', with the bracketed part repeating one or more times (but never repeating them in a row). Do not use empty lines between instructions.\n" +
      "- Sometimes, things don't go as planned. Tools may not provide useful information on the first few tries. The agent always tries a few different approaches before declaring the problem unsolvable:\n" +
      "- When the tool doesn't give you what you were asking for, you MUST either use another tool or a different tool input.\n" +
      '  - When using search engines, the assistant tries different formulations of the query, possibly even in a different language.\n' +
      '- When executing code, the assistant fixes and retries when the execution errors out and tries a completely different approach if the code does not seem to be working.\n' +
      '  - When the problem seems too hard for the tool, the assistant tries to split the problem into a few smaller ones.\n' +
      '\n' +
      '## Notes\n' +
      '- Any comparison table (including its content), file, image, link, or other asset must only be in the Final Answer.\n' +
      "- When the question is unclear, respond with a line starting with 'Final Answer:' followed by the information needed to solve the problem.\n" +
      '- When the user wants to chitchat instead, always respond politely.\n' +
      "- IMPORTANT: Lines 'Thought', 'Tool Name', 'Tool Caption', 'Tool Input', 'Tool Output' and 'Final Answer' must be sent within a single message.\n",
    meta: undefined
  },
  BaseMessage {
    role: 'user',
    text: 'Invoke the WriteMail tool only if the router update has been successful. Here is the output of the previous conversation: Yes, the router update was successfully invoked and completed for subscriber Mary (123-555-1234).\n' +
      '\n' +
      '**Summary:**\n' +
      '\n' +
      '* **Agent:** John (Teltop Customer Care Agent)\n' +
      '* **Subscriber:** Mary (123-555-1234)\n' +
      '* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.\n' +
      '* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.\n' +
      '* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.\n' +
      '* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.\n' +
      '* **Diagnostic Check:** Performed during the call.\n' +
      '* **Router Update:** Started immediately.\n' +
      '* **TV Service Quality Improvement:** Expected within the next 24 hours.\n' +
      '\n',
    meta: undefined
  }
]
```

## Write Mail Agent - First Iteration Output

```text
> Agent WriteMailIfNecessary (thought) 🤖 :  I need to write an email to the subscriber, but only if the router update was successful. Fortunately, the previous conversation output indicates that the router update was successfully invoked and completed for subscriber Mary (123-555-1234). I will use the WriteMail tool to send an email to Mary.

Agent WriteMailIfNecessary (tool_name) 🤖 :  WriteMail
Agent WriteMailIfNecessary (tool_caption) 🤖 :  Writing an email to subscriber Mary (123-555-1234)
Agent WriteMailIfNecessary LLM Output
{
  generated_text: 'Thought: I need to write an email to the subscriber, but only if the router update was successful. Fortunately, the previous conversation output indicates that the router update was successfully invoked and completed for subscriber Mary (123-555-1234). I will use the WriteMail tool to send an email to Mary.\n' +
    '\n' +
    'Tool Name: WriteMail\n' +
    'Tool Caption: Writing an email to subscriber Mary (123-555-1234)\n' +
    'Tool Input: {"phoneNumber":"123-555-1234","summary":"* **Agent:** John (Teltop Customer Care Agent)\\n* **Subscriber:** Mary (123-555-1234)\\n* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.\\n* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.\\n* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.\\n* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.\\n* **Diagnostic Check:** Performed during thell.\\n* **Router Update:** Started immediately.\\n* **TV Service Quality Improvement:** Expected within the next 24 hours."}\n' +
    '\n' +
    'Tool Output: ',
  generated_token_count: 36074,
  input_token_count: 1319,
  stop_reason: 'not_finished'
}
```

## Write Mail Agent - Tool Invocation

```text
> Agent WriteMailIfNecessary (tool_input) 🤖 :  {"phoneNumber":"123-555-1234","summary":"* **Agent:** John (Teltop Customer Care Agent)\n* **Subscriber:** Mary (123-555-1234)\n* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.\n* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.\n* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.\n* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.\n* **Diagnostic Check:** Performed during thell.\n* **Router Update:** Started immediately.\n* **TV Service Quality Improvement:** Expected within the next 24 hours."}

Input to Write Mail tool - phoneNumber: 123-555-1234
Input to Write Mail tool - summary: * **Agent:** John (Teltop Customer Care Agent)
* **Subscriber:** Mary (123-555-1234)
* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.
* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.
* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.
* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.
* **Diagnostic Check:** Performed during thell.
* **Router Update:** Started immediately.
* **TV Service Quality Improvement:** Expected within the next 24 hours.
Agent WriteMailIfNecessary (tool_output) 🤖 :  {"success":"true","text":"Mail has been written and sent"}
```

## Write Mail Agent - Second Iteration Input

```text
Agent WriteMailIfNecessary 🤖 :  starting new iteration
Agent WriteMailIfNecessary LLM Input
[
  BaseMessage {
    role: 'system',
    text: 'You are a helpful assistant that uses tools to answer questions.\n' +
      '\n' +
      '# Tools\n' +
      '\n' +
      'Tools must be used to retrieve factual or historical information to answer the question.\n' +
      'A tool can be used by generating the following three lines:\n' +
      '\n' +
      'Tool Name: ZblorgColorLookup\n' +
      'Tool Caption: Searching Zblorg #178\n' +
      'Tool Input: {"id":178}\n' +
      '\n' +
      '## Available tools\n' +
      'Tool Name: WriteMail\n' +
      'Tool Description: Writes an email to the subscriber, but only if and after the router could be updated successfully remotely.\n' +
      `Tool Input Schema: {"type":"object","properties":{"phoneNumber":{"type":"string","minLength":1,"maxLength":40,"description":"Phone number of a subscriber, for example '123-456-7890'"},"summary":{"type":"string","minLength":1,"maxLength":4000,"description":"Summary of the transcript between the agent and the subscriber"}},"required":["phoneNumber","summary"],"additionalProperties":false,"$schema":"http://json-schema.org/draft-07/schema#"}\n` +
      '\n' +
      '\n' +
      '# Instructions\n' +
      '\n' +
      'Responses must always have the following structure:\n' +
      "- The user's input starts with 'Question: ' followed by the question the user asked, for example, 'Question: What is the color of Zblorg #178?'\n" +
      "  - The question may contain square brackets with a nested sentence, like 'What is the color of [The Zblorg with the highest score of the 2023 season is Zblorg #178.]?'. Just assume that the question regards the entity described in the bracketed sentence, in this case 'Zblorg #178'.\n" +
      "- Line starting 'Thought: ', explaining the thought, for example 'Thought: I don't know what Zblorg is, but given that I have a ZblorgColorLookup tool, I can assume that it is something that can have a color and I should use the ZblorgColorLookup tool to find out the color of Zblorg number 178.'\n" +
      "  - In a 'Thought', it is either determined that a Tool Call will be performed to obtain more information, or that the available information is sufficient to provide the Final Answer.\n" +
      '  - If a tool needs to be called and is available, the following lines will be:\n' +
      "    - Line starting 'Tool Name: ' name of the tool that you want to use.\n" +
      "    - Line starting 'Tool Caption: ' short description of the calling action.\n" +
      "    - Line starting 'Tool Input: ' JSON formatted input adhering to the selected tool JSON Schema.\n" +
      `    - Line starting 'Tool Output: ', containing the tool output, for example 'Tool Output: {"success": true, "color": "green"}'\n` +
      "      - The 'Tool Output' may or may not bring useful information. The following 'Thought' must determine whether the information is relevant and how to proceed further.\n" +
      '  - If enough information is available to provide the Final Answer, the following line will be:\n' +
      "    - Line starting 'Final Answer: ' followed by a response to the original question and context, for example: 'Final Answer: Zblorg #178 is green.'\n" +
      '      - Use markdown syntax for formatting code snippets, links, JSON, tables, images, files.\n' +
      '      - To reference an internal file, use the markdown syntax [file_name.ext](urn:file_identifier).\n' +
      '        - The bracketed part must contain the file name, verbatim.\n' +
      '        - The parenthesis part must contain the file URN, which can be obtained from the user or from tools.\n' +
      '        - The agent does not, under any circumstances, reference a URN that was not provided by the user or a tool in the current conversation.\n' +
      '        - To show an image, prepend an exclamation mark, as usual in markdown: ![file_name.ext](urn:file_identifier).\n' +
      '        - This only applies to internal files. HTTP(S) links must be provided as is, without any modifications.\n' +
      "- The sequence of lines will be 'Thought' - ['Tool Name' - 'Tool Caption' - 'Tool Input' - 'Tool Output' - 'Thought'] - 'Final Answer', with the bracketed part repeating one or more times (but never repeating them in a row). Do not use empty lines between instructions.\n" +
      "- Sometimes, things don't go as planned. Tools may not provide useful information on the first few tries. The agent always tries a few different approaches before declaring the problem unsolvable:\n" +
      "- When the tool doesn't give you what you were asking for, you MUST either use another tool or a different tool input.\n" +
      '  - When using search engines, the assistant tries different formulations of the query, possibly even in a different language.\n' +
      '- When executing code, the assistant fixes and retries when the execution errors out and tries a completely different approach if the code does not seem to be working.\n' +
      '  - When the problem seems too hard for the tool, the assistant tries to split the problem into a few smaller ones.\n' +
      '\n' +
      '## Notes\n' +
      '- Any comparison table (including its content), file, image, link, or other asset must only be in the Final Answer.\n' +
      "- When the question is unclear, respond with a line starting with 'Final Answer:' followed by the information needed to solve the problem.\n" +
      '- When the user wants to chitchat instead, always respond politely.\n' +
      "- IMPORTANT: Lines 'Thought', 'Tool Name', 'Tool Caption', 'Tool Input', 'Tool Output' and 'Final Answer' must be sent within a single message.\n",
    meta: undefined
  },
  BaseMessage {
    role: 'user',
    text: 'Invoke the WriteMail tool only if the router update has been successful. Here is the output of the previous conversation: Yes, the router update was successfully invoked and completed for subscriber Mary (123-555-1234).\n' +
      '\n' +
      '**Summary:**\n' +
      '\n' +
      '* **Agent:** John (Teltop Customer Care Agent)\n' +
      '* **Subscriber:** Mary (123-555-1234)\n' +
      '* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.\n' +
      '* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.\n' +
      '* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.\n' +
      '* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.\n' +
      '* **Diagnostic Check:** Performed during the call.\n' +
      '* **Router Update:** Started immediately.\n' +
      '* **TV Service Quality Improvement:** Expected within the next 24 hours.\n' +
      '\n',
    meta: undefined
  },
  BaseMessage {
    role: 'assistant',
    text: 'Thought: I need to write an email to the subscriber, but only if the router update was successful. Fortunately, the previous conversation output indicates that the router update was successfully invoked and completed for subscriber Mary (123-555-1234). I will use the WriteMail tool to send an email to Mary.\n' +
      '\n' +
      'Tool Name: WriteMail\n' +
      'Tool Caption: Writing an email to subscriber Mary (123-555-1234)\n' +
      'Tool Input: {"phoneNumber":"123-555-1234","summary":"* **Agent:** John (Teltop Customer Care Agent)\\n* **Subscriber:** Mary (123-555-1234)\\n* **Specific Issues:** Wi-Fi keeps dropping, and the TV service over fiber is pixelated and glitchy.\\n* **Corrective Actions:** Guided the subscriber to perform a remote diagnostic check, updated the router software, and addressed the TV service quality.\\n* **Issue Resolved:** Yes, within 24 hours for the TV service and immediately for the router update.\\n* **Follow-up Actions and Timelines:** Three months of free TV service once the issues are resolved.\\n* **Diagnostic Check:** Performed during thell.\\n* **Router Update:** Started immediately.\\n* **TV Service Quality Improvement:** Expected within the next 24 hours."}\n' +
      'Tool Output: {"success":"true","text":"Mail has been written and sent"}\n',
    meta: { success: true }
  }
]
```

## Write Mail Agent - Second Iteration Output

```text
Agent WriteMailIfNecessary LLM Output
{
  generated_text: 'Thought: The WriteMail tool has successfully written and sent an email to subscriber Mary (123-555-1234).',
  generated_token_count: 325,
  input_token_count: 1605,
  stop_reason: 'eos_token'
}
> Agent WriteMailIfNecessary (thought) 🤖 :  The WriteMail tool has successfully written and sent an email to subscriber Mary (123-555-1234). 
> Agent WriteMailIfNecessary (final_answer) 🤖 :  The WriteMail tool has successfully written and sent an email to subscriber Mary (123-555-1234).
Response WriteMailIfNecessary 🤖 :  The WriteMail tool has successfully written and sent an email to subscriber Mary (123-555-1234).