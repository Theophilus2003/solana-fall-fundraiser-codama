## Versions

anchor-cli 1.1.2 · solana-cli 3.1.10 · node v24.20.0 · @codama/cli 1.6.3 ·
@codama/renderers-js 2.5.0 · @solana/kit 8.3.0 · surfpool 1.6.0

## TODO 3

Required: `fundraiser`, `vault`. Optional (auto-resolved): `contributorAccount`,
`contributorAta`, `tokenProgram`, `systemProgram`.

`contribute`'s `fundraiser` PDA is seeded on `fundraiser.maker`, a field
read from the account itself. Codama would need to already have the
`Fundraiser` account decoded in order to read `.maker` out of it and derive
its own address, a circular dependency it can't resolve on its own, so the
caller has to supply it directly. `initialize`'s `fundraiser` PDA, by
contrast, is seeded on `maker` directly, a plain account the caller
already holds a reference to, so Codama can call `findFundraiserPda` for
that instruction without any help. `contributorAccount` and
`contributorAta` are optional in `contribute` because their seeds
(`fundraiser` and `contributor`/`mint`) are all accounts the caller
already passed in or can derive independently, no circular read required.

## Bonus

attempted, passing. Built the instruction with `getContributeInstructionAsync`
(same four required inputs as TODO 3), converted it with
`tests/helpers/kit-adapter.ts`'s `toWeb3Instruction`, and sent it through
`provider.sendAndConfirm`. The vault balance grew by exactly `AMOUNT`.

## One thing that surprised me

The `pdas/` folder does contain a working `findFundraiserPda`, since
`initialize` can use it. It looks, at a glance, like the exact same
function `contribute` should be able to call too, but it can't, because
the two instructions derive the same-looking account from genuinely
different seeds. The account name being identical across instructions
made the distinction easy to miss until I actually diffed the two `seeds
= [...]` lines side by side.
