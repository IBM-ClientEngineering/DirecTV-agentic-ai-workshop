## Campaign Approval Process Workflow with Watsonx Orchestrate

> **Prerequisites:** Complete [Lab 1: Campaign Creation](../Lab-1-Campaign-Creation/README.md) before starting this lab.

## Table of Contents

- [1. Introduction](#introduction)
- [2. Email Campaign Approval Agent](#email-campaign-approval-agent)
  - [2.1 Creating necessary tools](#creating-tools)
  - [2.2 Create Agentic Workflow](#create-agentic-workflow)
  - [2.3 Creating the agent](#creating-the-agent)
  - [2.4 Importing the tools](#importing-the-tools)
  - [2.5 Update Agent Behavior](#update_agent_behavior)
  - [2.6 Deploy the Agent](#deploy-agent)
- [3. Testing the Agent](#testing-the-agent)
- [4. Summary](#summary)

## Downloadables

Ensure you download the below file and keep them handy since you will be using them as part of the lab.

- [lab2-tools-openapi.json](./artifacts/lab2_tools_openapi.json)

<details open id="introduction">
<summary><h2>1. Introduction</h2></summary>

Lab 1 established the campaign creation flow — transforming a marketing brief into a fully drafted email campaign, complete with a targeted audience segment, a personalized email template, and a campaign record submitted for review.

However, before any campaign communication reaches a DIRECTV subscriber, it must pass through a structured human review process. This ensures that all AI-generated content meets brand standards and campaign objectives before it is delivered to customers.

This lab focuses on building that review and approval experience within IBM watsonx Orchestrate.

As part of this lab, you will configure the below component:

- **An Approval Workflow** — a watsonx Orchestrate no-code Agentic workflow that fetches the pending approval records for the reviewer and steers through the process of approval/rejection. Upon approval completes the campaign by personalising the template with customer centric information followed by delivering the email to the customers

Upon completion of this lab, the end-to-end campaign automation system will be fully operational. A campaign brief initiated in Lab 1 will progress through audience selection, customer profiling, email template generation, and human review — concluding with a formal approval decision recorded within IBM watsonx Orchestrate.

<!-- [← Back to Table of contents](#table-of-contents) -->

</details>

<details open id="email-campaign-approval-agent">
<summary><h2>2. Email Campaign Approval Agent</h2></summary>

In this section you will deal with the step by step guide to build the Approval process. Begin with creating the tools necessary for this lab.

<details open id="creating-tools">
<summary><h3>2.1 Creating necessary tools</h3></summary>

1. Navigate to the `Build Agents` page

2. Click **All tools** then **Create Tool +**

   ![image.png](images/create_tool_2.png)

3. Select the **OpenAPI** tile

   ![image.png](images/select_openapi.png)

4. Click **Drag and drop an OpenAPI file here or click to upload.**

   ![image.png](images/drag_file.png)

5. Upload the **OpenAPI tool file** downloaded in the beginning : [lab2-tools-openapi.json](./artifacts/lab2_tools_openapi.json) and click **Next**.

   ![image.png](images/upload_file.png)

6. This page shows all the available tools, you will be using all the tools specified in the window.
   - `List Campaigns` tool - This tool will be used to list all the campaigns which are pending for review.
   - `Get Campaign details` tool - This tool will be used to get the details of a specific campaign.
   - `Submit approval decision` tool - This tool will be used to submit the approval decision for a specific campaign.
   - `Personalize approved template` tool - This tool will be used to personalize the approved template for the targeted customers.
   - `Deliver campaign emails` tool - This tool will be used to deliver the campaign emails to the targeted customers.

7. **Select the first checkbox** next to the **Name** column to select all the tools and click **Done**.

   ![image.png](images/select_tool1.png)

<!-- [← Back to Table of contents](#table-of-contents) -->

</details>

<details open id="create-agentic-workflow">
<summary><h3>2.2 Create no code Agentic Workflow</h3></summary>

In this section you will deal with the core part to of this lab which is to design the complete no code Agentic workflow for the approval of the campaigns.

1. Click **All tools** then **Create Tool +**

   ![image.png](images/create_tool_3.png)

2. Select the **Agentic Workflow** option, then **Start Building**

   ![image.png](images/workflow_selection.png)
   ![image.png](images/start_building.png)

3. Click the pencil icon next to **Edit details**.

   ![image.png](images/edit_details.png)

4. Provide a **Name** and **Description** for the workflow, then click **Save**.
   - Name:

     ```
     Approval Workflow
     ```

   - Description:

     ```
     This tool is designed to initiate the approval process for pending campaign submissions
     ```

   ![image.png](images/workflow_name.png)

5. You can start building the Agentic workflow by adding your first tool to fetch all the pending campaign submissions. Click the **Add +** button, then **Call a tool**

   ![image.png](images/add_first_tool.png)

6. Scroll down in the provided list of tools and select the **List campaigns** tool, Drag this and drop it on to the **Add +** button

   ![image.png](images/select_list_campaign_2.png)
   ![image.png](images/drag_list_campaigns.png)
   This tool will list out all the campaings that have been submitted for approval.

7. You now need to build a form for the approver to select a particular campaign, to do this you need to build a form component. Switch from "Tools" to "Flow Nodes" and Drag the **User Activity** Component onto the **+** symbol as shown below

   ![image.png](images/drag_ua.png)

8. Click **Add +** option inside the **User Activity** component and select **Add a form**

   ![image.png](images/add_form.png)

9. Click the **Edit symbol** and rename the component to `Select Campaign to review`

   ![image.png](images/image_(9).png)

10. Uncheck the **Cancel** Button Option as shown below, since you need only a single submit button to select the campaign.

    ![image.png](images/uncheck_cancel.png)

11. Click the **Add Field+** button, hover over **Collect from user** and then select **Single Choice**.

    ![image.png](images/select_single_choice.png)

12. Edit the label to `Select campaign` and select the **{x}** icon in the **Source Variable** field.

    ![image.png](images/select_var_form.png)

13. Click the **List campaigns** tool button and select the first **campaign_details** variable as shown below.

    ![image.png](images/select_var_form1.png)

14. Under **Appearance** select the **Table** option and click **Edit Columns**.

    ![appearance.png](images/select_var_form2.png)

15. Reorder the columns as shown below by dragging and moving the leftmost icon. Then disable the columns which you do not wish to present to the user by unchecking the **eye** icon. Click **Done**. the click on the **x** icon to close the component.

    ![image.png](images/select_var_form3.png)

16. You have displayed the list of campaigns and provided the approver a way to select a particular campaign. You now need to fetch the details for the campaign the approver has selected, to do this, go to the **Tools** tab as show below, scroll down to find the **Get campaign details** tools and drag this tool on the **+** icon below the form you just created.

    ![image.png](images/get_campaign1_2.png)

17. Click the **Get campaign details** tool you just added and click **Edit data mapping**

    ![image.png](images/get_campaign2_2.png)

18. Click the **{x}** icon, choose the **Select Campaign to review** (which is basically the previous form we created) and select the first varaible which says `Select Campaign.campaign_id`

   ![alt text](images/get_campaign_details_updated.png) 

19. You have now fetched the selected campaign details, now you need to display the campaign template generated for the approver to review. To do this, click the **+** icon below the tool you just added and select the **Add a form** option.

    ![image.png](images/approval_form1.png)

20. Rename the form name to **Review Campaign details**. Change the labels of the **Primary action** button and **Cancel** button to **Approve** and **Reject** respectively.

    ![image.png](images/approval_form2.png)

21. Click the **Add field +** button, hover over **Present to user** and select the **Message** option.

    ![image.png](images/approval_form3.png)

22. Paste the below string into the message box, You can alternatively even select the variables directly from the tool output variables option.

    ```
    {flow["User activity 1"]["Get campaign details — DIRECTV Campaign Management"].output.current_template.content.subject}

    {flow["User activity 1"]["Get campaign details — DIRECTV Campaign Management"].output.current_template.content.greeting}

    {flow["User activity 1"]["Get campaign details — DIRECTV Campaign Management"].output.current_template.content.body}

    {flow["User activity 1"]["Get campaign details — DIRECTV Campaign Management"].output.current_template.content.closing}

    {flow["User activity 1"]["Get campaign details — DIRECTV Campaign Management"].output.current_template.content.ps}
    ```

    ![image.png](images/approval_form4.png)

23. You can close this component. Now you have provided a form to the approver to review the template of the campaign and **Approve** or **Reject** it. Draw the Reject path connection connecting the **Reject** node to the **End** node if it does not exist as shown below.

    ![image.png](images/approval_form5.png)

24. Now you need to update the decision of the approver to the backend based on the approver's choice. To do this, go to **Tools** tab and drag the **Submit approval decision** tool to both the **Approve** and **Reject** paths as shown below

    <table>
        <tr>
        <td><img src="images/submit_approval1_2.png"/></td>
        <td><img src="images/submit_approval2_2.png"/></td>
        </tr>
    </table>

25. Click the **Submit approval decision** tool on the **Approve** path and click **Edit Data mapping** button. Click the **{x}** icon for the **campaign_id** variable. Select the **Select campaign to review** item from the left menu and click the second **campaign_id** vairable.

    ![image.png](images/submit_approval3.png)
    ![image.png](images/submit_approval4.png)
    ![image.png](images/submit_approval5.png)

26. For remaining fields enter the following values (Ensure you the copy the exact same value for the **decision** field):
    - **decision**: `APPROVED`
    - **reviewer**: `Orchestrator`
    - **reviewer_notes**: `Approved`
      ![image.png](images/submit_approval6.png)

27. Now click the **Submit approval decision** tool on the **Reject** path and click **Edit Data mapping** button. Click the **{x}** icon for the **campaign_id** variable. Select the **Select campaign to review** item from the left menu and click the second **campaign_id** vairable just like you did for the Approve path.

    ![image.png](images/submit_approval7.png)
    ![image.png](images/submit_approval4.png)
    ![image.png](images/submit_approval5.png)

28. For remaining fields enter the following values (Ensure you the copy the exact same value for the **decision** field):
    - **decision**: `REJECTED`
    - **reviewer**: `Orchestrate`
    - **reviewer_notes**: `Rejected`
      ![image.png](images/submit_approval8.png)

29. Now that you have updated the backend database with the approver decision, we can now proceed to the next step where upon approval, we personalize the emails to a customer. To do this from the **Tools** tab, drag the **Personalize approved template** tool onto the **+** icon in the **approval** path as shown below.

    ![image.png](images/personalise1.png)

30. Click the **Personalize approved template** tool and select the **Edit data mapping** option.

    ![image.png](images/personalise2_2.png)

31. Click the **{x}** icon for the **campaign_id** variable. Select the **Select campaign to review** item from the left menu and click the second **campaign_id** vairable.

    ![image.png](images/personalise3.png)

32. Now that we are personalising the emails for target customers, you can inform the approver once this process is complete. To do this click the **+** icon below **Personalize approved template** tool, hover over **Present to user** and select **Message** option.

    ![alt text](images/present_to_user_updated.png)

33. Click the **Message** block and paste the below content.

    ```
    The campaign template has been personalized for {flow["User activity 1"]["Personalize approved template — DIRECTV Marketing Personalization"].output.personalized_count} customer(s)

    Each email is being tailored with their personal details.
    ```

    ![alt text](images/step_33.png) 

34. Now that you have personalized the email template for each customer. You need to deliver the emails to the customers. To do this from the **Tools** tab, drag the **Deliver Campaign emails** tool onto the **+** icon in the **approval** path as shown below.

  ![alt text](images/deliver_campaign_email_details_updated.png) 

35. Click the **Deliver Campaign emails** tool and select the **Edit data mapping** option.

    ![image.png](images/deliver2.png)

36. Click the **{x}** icon for the **campaign_id** variable. Select the **Select campaign to review** item from the left menu and click the second **campaign_id** vairable. Set the **dry_run** variable to **True**.

    ![alt text](images/step_36.png)
    <!-- ![image.png](images/personalise4.png) -->

37. Since you are delivering the emails directly to the customers, you can inform the approver once this process is complete. To do this click the **+** icon below **Deliver Campaign emails** tool, hover over **Present to user** and select **Message** option.

   ![alt text](images/step_37.png)

38. Click the **Message** block and paste the below content.

    ```
    {flow["User activity 1"]["Personalize approved template — DIRECTV Marketing Personalization"].output.personalized_count} Email(s) have been sent successfully.

    Campaign {flow["User activity 1"]["Deliver campaign emails — Salesforce Marketing Cloud"].output.campaign_name} is now complete!
    ```

    ![image.png](images/deliver5.png)

    You are now complete with your entire **Approval Workflow**. Next step would be to add this workflow to an agent which will invoke this workflow upon Approver action.

[← Back to Table of contents](#table-of-contents)

</details>

<details open id="creating-the-agent">
<summary><h3>2.3 Creating the agent</h3></summary>

In this section you will deal with creating the Email Campaign Approval Agent which will invoke the no-code Agentic approval workflow we created in the previous section.

1. Go to the **Manage Agents** page, click the **Create Agent +** button.

   ![image.png](images/agent1_2.png)

2. Select **Create from scratch** and enter the below details.
   - **Name**:

   ```
   Email Campaign Approval Agent
   ```

   - **Description**:

   ```
   This agent is used to initiate the approval flow for the campaign template
   ```

   ![image.png](images/agent2.png)

3. Under the **Quick Start Prompts** section, delete the existing prompts and add the below provided prompt.
   ```
   Check for my pending approvals
   ```
   ![image.png](images/agent3.png)

[← Back to Table of contents](#table-of-contents)

</details>

<details open id="importing-the-tools">
<summary><h3>2.4 Importing the tools</h3></summary>

1. Scroll down to the **Toolset** section and click **Add tool +** button.

   ![image.png](images/agent4.png)

2. Select the **Local Instance** option from the provided window.

   ![image.png](images/agent5.png)

3. Select the **Approval Workflow** you created previously by clicking on the checkbox and select **Add to agent**.

   ![image.png](images/agent6.png)

[← Back to Table of contents](#table-of-contents)

</details>

<details open id="update_agent_behavior">
<summary><h3>2.5 Update Agent Behavior</h3></summary>

1. Scroll down to the **Behavior** section and copy the below content on the the textbox

   ```
   You are an approvals assistant agent. Your job is to help users review their pending campaign approvals.

   ## Behavior Instructions

   ### Trigger Condition
   When the user says anything related to checking pending approvals (e.g., "check my pending approvals", "show my approvals", "what needs approval"), immediately call the "Approval  Workflow" tool.

   ### After Tool Execution
   Once the "Approval Workflow" tool has completed execution, always follow up with:
   "Done! Would you like to check for any other pending approvals?"

   ### Loop Logic
   - If the user responds **Yes** (or any affirmative): Call the "Approval Workflow" tool again and repeat the loop.
   - If the user responds **No** (or any negative): End the conversation warmly. Example closing message:
   "Thank you for reviewing your approvals! Have a wonderful day! "

   ## Rules
   - Never skip calling the tool when approvals are requested.
   - Never end the conversation abruptly — always ask the follow-up question after each tool run.
   - Only end the session when the user explicitly says they do not want to check further.
   - Keep all responses concise and professional.
   ```

   ![image.png](images/agent7.png)

   You have now completed the creation of the agent, It is now ready to be deployed and tested.

[← Back to Table of contents](#table-of-contents)

</details>

<details open id="deploy-agent">
<summary><h3>2.6 Deploy the Agent</h3></summary>

In this section you will be dealing with deploying the agent with the Agentic Approval workflow. Follow the below steps.

1. Click the **Deploy** button on the top right corner to deploy the agent.

   ![image.png](images/deploy1.png)

2. You can see the complete details about the agent such as profile, Included tools and the Behavior instructions. Click the **Deploy** button to deploy the agent.

   ![image.png](images/deploy2.png)

3. It will take a few seconds for the agent to be deployed, Once the agent is successfully deployed you be seeing the **Live** status as shown below.

   ![image.png](images/deploy3.png)

[← Back to Table of contents](#table-of-contents)

</details>

</details>

<details open id="testing-the-agent">
<summary><h2>3. Testing the Agent</h2></summary>

- Now that the agent has been successfully deployed, you can test the complete flow. As part of Lab 1 you would have receieved an email as shown below.
  ![image.png](images/test3.png)

  Note the campaign ID present in the received email.

1. Click the **Chat** button on from the left hamburger menu options.

   ![image.png](images/test1.png)

2. Ensure the **Email Campaign Approval Agent** is selected from the list of agents in the dropdown menu.

   ![image.png](images/test2_2.png)

3. Click the `Check for my pending approvals` button.

4. This will retreive all the list of pending submissions from the campaign creation team. Select the campaign ID corresponding to the email received earlier and click **Submit** (You can also search for the campaign ID in the top search bar present in the table).

   ![image.png](images/test4.png)

5. This will give us the details of the campaign template for the campaign that was generated as below.

   ![alt text](images/step_6.png) 

6. Upon review it is upto the reviewer to accept or reject the campaign. For testing purpose you go ahead and **Approve the campaign**

7. Upon approval you can see that the next steps of personalising and delivering the campaign to the target customers will take place.

  ![alt text](images/step_7.png)

8. At the end of the campaign you can see that the agent prompts the approver if they wish to check for other pending requests, if the approver confirms, the process will start again.

9. Below is an example of how a personalized customer email would look after the campaign is approved and delivered:

   ![Sample Customer Email](images/sample_customer_email.png)

[← Back to Table of contents](#table-of-contents)

</details>

<details open id="summary">
<summary><h2>4. Summary</h2></summary>

Congratulations on completing **Lab 2 — Campaign Approval!** 🎉

You have successfully implemented the human-in-the-loop review layer that sits between AI-generated campaign content and real DIRECTV subscribers — bringing the full campaign automation flow to completion.

---

### What You Accomplished

- Configured a **No-code Agentic Approval Workflow** in IBM watsonx Orchestrate that automatically notifies the designated reviewer when a campaign is pending, presents the draft email template for evaluation, and records the final decision against the campaign record.
- Built and deployed the **Campaign Approval Agent** that guides the reviewer through the evaluation process and ensures the outcome is accurately captured in the system.
- Executed a full end-to-end approval run — receiving a campaign submitted from Lab 1, reviewing the draft template, and recording a formal **Approved** or **Rejected** decision within watsonx Orchestrate.

---

### Key Concepts You Learned

- **Human-in-the-loop (HITL) workflows** — How to introduce a structured human review checkpoint between AI-generated content and real-world delivery using watsonx Orchestrate's agentic workflow tooling.
- **Agentic workflow configuration** — How to set up and connect an no-code Agentic approval workflow in watsonx Orchestrate Workflow Builder so it triggers automatically at the right stage of the flow.
- **Approval agent design** — How to configure an agent that manages a review interaction, presents relevant campaign information to the reviewer, and captures a structured decision.
- **Campaign lifecycle management** — How an approval decision propagates through the system, updating campaign status from `pending_approval` to `approved` or `rejected`.

[← Back to Table of contents](#table-of-contents)

</details>

[← Back to Lab 1](../Lab-1-Campaign-Creation/README.md) | [← Back to Main Page](../README.md)
