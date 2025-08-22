# C++

Goal: real-time, low-latency audio processing; avoid anything that can block or allocate on the audio thread.

## Audio-thread rules
- No heap allocations, file IO, iostreams or blocking syscalls on audio thread.
- Mark hot functions noexcept; do not throw across thread boundaries.
- No blocking locks (std::mutex, condition_variable) on audio thread.

## Concurrency & data passing
- Use SPSC lock-free FIFOs (JUCE AbstractFifo, folly ProducerConsumerQueue, or custom) for thread transfer.
- Use std::atomic for scalar flags; document memory-ordering; prefer relaxed where safe.

## Performance patterns
- Prefer templates/compile-time polymorphism in per-sample/per-block hot paths; avoid virtual calls there.
- Preallocate and reuse DSP buffers in prepareToPlay; size for max expected block.
- Keep memory contiguous and SIMD-friendly (aligned arrays, reserve vectors).

## Tooling & CI
- CI: Release (-O3) and Debug builds; micro-benchmark processBlock at target rates.
- Enforce clang-tidy/clang-format and static analysis in CI.

## Quick checklist (review)
- processBlock allocates? No.
- Locks on audio thread? No.
- Parameter updates lock-free and smoothed? Yes.

## Minimal contract
- Inputs: preallocated float/double buffers, channel count, numSamples.
- Output: in-place only; no heap or locks; return within RT budget.
- Error handling: signal non-realtime logging via atomics or ring buffer; do not log on audio thread.
