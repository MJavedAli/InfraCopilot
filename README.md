# InfraCopilot
an LLM-driven infrastructure assistant for real-time infra querying, IaC generation, and drift diagnostics using Terraform state, AWS Config, and CloudWatch.

## Overview
InfraCopilot bridges the gap between IaC, cloud state, and observability by enabling engineers to interact with infrastructure using natural language.

It answers questions like:

“List EC2 instances missing cost-allocation tags and generate Terraform fixes.”
“Show non-compliant resources from AWS Config with remediation hints.”
“Summarize error spikes from CloudWatch logs for Lambda XYZ.”

## Key Capabilities
Feature	Description
- Natural language infra queries	Ask about infrastructure state, compliance, and performance
- Terraform state + plan parsing	Extract module/resource definitions & detect drift
- AWS Config integration	Pull compliance status & config history via API
- CloudWatch Logs Insights	Retrieve real-time logs/metrics & anomaly context
- LLM reasoning + LangChain	Agent tool routing, grounding, & policy checks
- IaC generation	Terraform snippet suggestions (never auto-apply)
- Guardrails	Read-only ops, schema validation, command filtering

## Architecture
```
┌────────────┐     Query      ┌──────────────┐
│   User     ├──────────────► │ LangChain    │
└─────┬──────┘  (NL request)  │ Agent Router │
      │                       └─────┬────────┘
      │      Function Calls         │
      ▼                             ▼
┌──────────┐     ┌───────────────────────┐   ┌─────────────────┐
│ Terraform│◄───►│ AWS Config API        │◄─►│ CloudWatch Logs │
│ State &  │     │ (read-only)           │   │ Insights        │
│ Plan     │     └───────────────────────┘   └─────────────────┘
└────┬─────┘             │
     │ Parsed context    │ Structured output + TF suggestion
     ▼                   ▼
┌────────────────────────────────────────────────────────┐
│        GPT-4/5 + Policies (No destructive ops)         │
└────────────────────────────────────────────────────────┘
```

## Design Principles

- Grounded outputs only — no hallucinated infra
- Least-privilege credentials
- Zero write operations
- Plan-level IaC suggestions, never apply
- Explain before output (reasoning transparency)

## Example Prompt
```
Which EC2 instances violate tagging policy? 
Provide context, owners, and Terraform code to fix tags.
```

### Sample Output 

```
Non-compliant instances: 3
- i-0abc123: missing `team` and `env`
- i-0987xyz: missing `cost-center`

Terraform Fix:
resource "aws_instance" "app_1" {
  tags = {
    team        = "platform"
    env         = "staging"
    cost-center = "FIN-PLAT"
  }
}

```
## Security

- Read-only API keys
- No apply, delete, terminate
- All LLM tool calls validated against policy schema

## License
This project is licensed under the Apache 2.0 License — see the [LICENSE](./LICENSE) file for details.

---

**Copyright © 2025 Javed Ali**



