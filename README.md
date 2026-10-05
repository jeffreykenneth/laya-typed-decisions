# Laya Typed-Decisions

A private GitHub repository for the `typed-decisions` Laya checkpoint. The model files are distributed as a GitHub Release asset so the 842,609,220-byte weights file is not stored in Git history.

## Download the model

Requires GitHub CLI authentication with access to this private repository, plus Python 3.10+.

```bash
gh release download laya-typed-decisions-2026-10-05 \
  --repo jeffreykenneth/laya-typed-decisions \
  --pattern laya-typed-decisions-2026-10-05.zip
python -m zipfile -e laya-typed-decisions-2026-10-05.zip .
```

The archive extracts to `typed-decisions/`:

```text
typed-decisions/
├── model.safetensors
├── rl_agent_config.json
├── encoder/
│   └── config.json
└── tokenizer/
    ├── tokenizer.json
    └── tokenizer_config.json
```

## Quickstart

Install the Laya runtime, then run this example from the directory containing `typed-decisions/`:

```bash
python -m pip install laya
```

```python
import laya

agent = laya.load("./typed-decisions", device="auto")

state = {
    "subject": "Duplicate charge on invoice #4411",
    "body": "We were billed twice for March. Please refund the duplicate today.",
}
questions = {
    "department": {
        "type": "choice",
        "instructions": "Which department should handle this request?",
        "criteria": {
            "billing": "invoices, payments, and refunds",
            "technical": "bugs and system errors",
            "other": "everything else",
        },
    },
    "urgency": {
        "type": "score",
        "instructions": "How urgent is this request?",
        "criteria": ["not urgent", "soon", "blocking"],
    },
    "refund_requested": {
        "type": "noul",
        "instructions": "Does the user explicitly request a refund?",
    },
}

result = agent.predict(state, questions)
print(result["answers"]["department"]["choice"])
print(result["answers"]["urgency"]["score"])
print(result["answers"]["refund_requested"]["noul"])
```

`laya.load()` accepts the extracted local checkpoint directory. On GPU hosts, install a PyTorch build that matches your CUDA setup; `device="auto"` selects an available accelerator or CPU.

## Source and provenance

- Hugging Face source: [convaiinnovations/laya](https://huggingface.co/convaiinnovations/laya/tree/main/typed-decisions)
- Source revision: `458d7563c5cab85ff9f7f6e06cf2dd166fb697e2`
- Model license shown by the source model card: Apache-2.0
- `model.safetensors` SHA-256: `4fa56de72383a9d3efa9cfa78955733c81b9fc8067a587ca4beb82c78107a24e`
