# Hi, I'm Yuan Lei

I work on inference-runtime reliability, distributed failure diagnosis, and
reproducible systems evidence. My current focus is turning failures such as
“the process is alive, but inference has stopped” into bounded claims that can
be independently checked and used in upstream review.

## Featured release: vLLM Runtime Reliability Lab v0.2.0

[vLLM Runtime Reliability Lab](https://github.com/jackLei0901/vllm-runtime-reliability-lab)
is an evidence lab for inference failures, not a monitoring platform or issue
collection.

The v0.2.0 release packages an independent base/fix validation of reported
[vLLM #53859](https://github.com/vllm-project/vllm/issues/53859) and proposed
[PR #53883](https://github.com/vllm-project/vllm/pull/53883): EngineCore stayed
alive and `/health` remained 2xx while token progress stopped under
deterministic event-queue backpressure. Under the same trigger, the fix
preserved progress while dropping four event batches.

Replay the published evidence without a GPU, vLLM, or PyTorch:

```bash
git clone --branch v0.2.0 https://github.com/jackLei0901/vllm-runtime-reliability-lab.git
cd vllm-runtime-reliability-lab
python -m pip install .
vllm-dfx replay results/vllm-zmq-backpressure-stage1-r3-20260916
```

The replay verifies archived evidence and does not rerun the GPU experiment or
claim production readiness.

- [v0.2.0 release](https://github.com/jackLei0901/vllm-runtime-reliability-lab/releases/tag/v0.2.0)
- [Failures that never reach the supervisor](https://github.com/jackLei0901/vllm-runtime-reliability-lab/blob/main/docs/FAILURES_THAT_NEVER_REACH_THE_SUPERVISOR.md)
  ([中文](https://github.com/jackLei0901/vllm-runtime-reliability-lab/blob/main/docs/FAILURES_THAT_NEVER_REACH_THE_SUPERVISOR.zh-CN.md))
- [English technical note](https://github.com/jackLei0901/vllm-runtime-reliability-lab/blob/main/docs/V0.2_LAUNCH_POST.md)
- [中文技术说明](https://github.com/jackLei0901/vllm-runtime-reliability-lab/blob/main/docs/V0.2_LAUNCH_POST.zh-CN.md)

## Evidence-backed investigations

- [PyTorch #196968](https://github.com/pytorch/pytorch/issues/196968) and
  [PR #197232](https://github.com/pytorch/pytorch/pull/197232): a missing Flight
  Recorder dump did not mean the rank was absent; the diagnostic producer had
  disappeared during communicator teardown.
- [PyTorch #196996](https://github.com/pytorch/pytorch/issues/196996): reduced an
  apparent distributed hang to a single-GPU FSDP2 mixed-gradient dtype failure.
- [vLLM #53859](https://github.com/vllm-project/vllm/issues/53859) and
  [PR #53883](https://github.com/vllm-project/vllm/pull/53883): independent
  health-green no-progress and base/fix validation, not a lab-originated bug.

The standard is simple: preserve what is known, distinguish missing evidence
from negative evidence, and return `undetermined` when the claim cannot be
supported.
