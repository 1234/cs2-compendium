# Experiments

Reproducible captures behind specific claims in this compendium.

Each experiment in this folder follows the same shape: a hypothesis, an exact
setup, a methodology, raw data tables, and a conclusion. Other Compendium
sections link back to the experiment that grounds a particular recommendation
instead of repeating the numbers inline. If you disagree with a conclusion,
you read the experiment, run the same A/B on your own hardware, and either
confirm or contradict it. That's how this compendium is supposed to evolve.

For the tools and methodology, see [measuring](../measuring/README.md).

---

## Index

| # | Title | Topic | Linked sections |
|---|---|---|---|
| 001 | [VRR vs fixed refresh when FPS << refresh](exp-001-vrr-vs-fixed-refresh.md) | G-Sync / OLED / cap strategy | [windows § VSync note](../windows/README.md#vsync-note-the-canonical-g-sync-compatible-recipe) |
| 002 | [HAGS on vs off on RTX 4090 + Reflex](exp-002-hags-rtx40-reflex.md) | HAGS architecture caveat | [windows § HAGS](../windows/README.md#4-hags-hardware-accelerated-gpu-scheduling) |
| 003 | [Process Lasso Core-0 exclude on 6 P-core SKU](exp-003-process-lasso-6p-core.md) | Intel hybrid affinity | [hardware § Intel-specific setting](../hardware/README.md#intel-specific-setting-modern-hybrid-scheduling) |
| 004 | [CS2 FPS ceiling on 14600K + DDR4-4000 (Gear 2)](exp-004-fps-ceiling-memory-bound.md) | Memory bottleneck pattern | [hardware § DDR4 Gear 1 vs Gear 2](../hardware/README.md#ddr4-gear-1-vs-gear-2) |
| 005 | [DDR4-3600 Gear 1 vs DDR4-4000 Gear 2 on 14600K (CS2)](exp-005-ddr4-3600-gear1.md) | DDR4 gear tuning / null result | [hardware § DDR4 Gear 1 vs Gear 2](../hardware/README.md#ddr4-gear-1-vs-gear-2) |

---

## Contributing an experiment

PR welcome. Use `exp-NNN-short-description.md`. The format is documented in
[measuring § Naming convention](../measuring/README.md). Document failures
and null results too, those are data.

---

← [Back to compendium](../README.md) → [Measuring](../measuring/README.md)
