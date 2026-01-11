GitHub Copilot supports multiple models, each with different strengths. Some models prioritize speed and cost-efficiency, while others are optimized for accuracy, reasoning, or working with multimodal inputs (like images and code together).

Depending on your Copilot plan and where you're using it—such as GitHub.com or an IDE—you may have access to different models.

> \[!NOTE]
>
> * Model availability is subject to change. Some models may be replaced or updated over time.
> * In Visual Studio Code you can add more models than those that are available by default with your Copilot subscription. See [Changing the AI model for GitHub Copilot Chat](/en/copilot/how-tos/use-ai-models/change-the-chat-model?tool=vscode#adding-more-models).

For all of the default AI models, input prompts and output completions run through GitHub Copilot's content filters for harmful, offensive, or off-topic content, and for public code matching when enabled.

## Supported AI models in Copilot

This table lists the AI models available in Copilot, along with their release status and availability in different modes.

> \[!Important] Complimentary access to Grok Code Fast 1 is continuing past the previously announced end time. A new end date has not been set. We may update or conclude this promotion at any time. [Regular pricing](/en/copilot/reference/ai-models/supported-models#model-multipliers) applies after the extension ends.

<div class="ghd-tool rowheaders">

| Model name | Provider | Release status | Agent mode | Ask mode | Edit mode |
| ---------- | -------- | -------------- | ---------- | -------- | --------- |
|            |          |                |            |          |           |

</div>

## Model retirement history

The following table lists AI models that have been retired from Copilot, along with their retirement dates and suggested alternatives.

<div class="ghd-tool rowheaders">

| Model name | Retired date | Suggested alternative |
| ---------- | ------------ | --------------------- |
|            |              |                       |

</div>

## Supported AI models per client

The following table shows which models are available in each client.

> \[!NOTE]
>
> * When you use Copilot Chat in supported IDEs, **Auto** will automatically select the best model for you based on availability. You can manually choose a different model to override this selection. See [About Copilot auto model selection](/en/copilot/concepts/auto-model-selection) and [Changing the AI model for GitHub Copilot Chat](/en/copilot/how-tos/use-ai-models/change-the-chat-model?tool=vscode).
> * GPT-5-Codex is supported in Visual Studio Code v1.104.1 or higher.
> * GPT-5.1-Codex and GPT-5.1-Codex-Mini are supported in:
>   * Visual Studio Code versions 1.104.1 or higher
>   * JetBrains, Copilot plugin versions 1.5.61 or higher
>   * Xcode, Copilot plugin versions 0.45.0 or later
>   * Eclipse, Copilot plugin versions 0.13.0 or later

<div class="ghd-tool rowheaders">

| Model | GitHub.com | Visual Studio Code | Visual Studio | Eclipse | Xcode | JetBrains IDEs |
| ----- | ---------- | ------------------ | ------------- | ------- | ----- | -------------- |
|       |            |                    |               |         |       |                |

</div>

## Supported AI models per Copilot plan

The following table shows which AI models are available in each Copilot plan. For more information about the plans, see [Plans for GitHub Copilot](/en/copilot/about-github-copilot/plans-for-github-copilot).

<div class="ghd-tool rowheaders">

| Available models in chat | Copilot Free | Copilot Pro | Copilot Pro+ | Copilot Business | Copilot Enterprise |
| ------------------------ | ------------ | ----------- | ------------ | ---------------- | ------------------ |
|                          |              |             |              |                  |                    |

</div>

## Model multipliers

Each model has a premium request multiplier, based on its complexity and resource usage. If you are on a paid Copilot plan, your premium request allowance is deducted according to this multiplier.

For more information about premium requests, see [Requests in GitHub Copilot](/en/copilot/managing-copilot/monitoring-usage-and-entitlements/about-premium-requests).

<div class="ghd-tool rowheaders">

| Model | Multiplier for **paid plans** | Multiplier for **Copilot Free** |
| ----- | ----------------------------- | ------------------------------- |
|       |                               |                                 |

</div>

## Next steps

* For task-based guidance on selecting a model, see [AI model comparison](/en/copilot/reference/ai-models/model-comparison).
* To configure which models are available to you, see [Configuring access to AI models in GitHub Copilot](/en/copilot/using-github-copilot/ai-models/configuring-access-to-ai-models-in-copilot).
* To learn how to change your current model, see [Changing the AI model for GitHub Copilot Chat](/en/copilot/using-github-copilot/ai-models/changing-the-ai-model-for-copilot-chat) or [Changing the AI model for GitHub Copilot inline suggestions](/en/copilot/how-tos/use-ai-models/change-the-completion-model).
* To learn more about Responsible Use and Responsible AI, see [Copilot Trust Center](https://copilot.github.trust.page/) and [Responsible use of GitHub Copilot features](/en/copilot/responsible-use-of-github-copilot-features).
* To learn how Copilot Chat serves different AI models, see [Hosting of models for GitHub Copilot Chat](/en/copilot/reference/ai-models/model-hosting).
