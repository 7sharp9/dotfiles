# JUCE (concise)

- Use JUCE (latest) for VST/AU/AAX; prefer CMake; target Windows + macOS; keep code modular and cross‑platform.

## Idiomatic guidelines

- Lifecycle & allocation
  - Do heavy setup in constructors or prepareToPlay; no heap allocations on the audio thread.
  - Never call GUI APIs from audio thread — use MessageManager::callAsync or signal the message thread via atomics/FIFO.

- Parameters & smoothing
  - Use AudioProcessorValueTreeState (APVTS). Read params lock‑free (atomic views or copy‑on‑write tokens) on audio thread.
  - Apply lightweight per‑sample smoothing (single‑pole / linear) in the audio callback to avoid zipper noise.

- DSP & modules
  - Prepare DSP (juce::dsp::ProcessorChain, FFT, etc.) in prepareToPlay; reuse buffers and objects.
  - Measure AudioProcessorGraph overhead before using in high‑rate inner loops.

- Thread safety
  - Do not invoke change listeners that touch GUI from audio thread; hand off via atomics/FIFO/message thread.

## Quick checklist
- Large allocations off audio thread? Yes.
- Parameter reads lock‑free and smoothed? Yes.
- GUI updates posted to message thread? Yes.
- DSP prepared in prepareToPlay and reused? Yes.
