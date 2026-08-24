# Content Guarantee Terms — handoff for Nisarg

Status: draft v1.0. All wording decisions are closed (Aug 20: contact = kscalzo@, amount wording = product price, experiences non-chargeable); the legal pass is with Julia. Copy below matches the rev 5 card screen (Pikora design) and the Payment method settings page. Live page: https://juliabenable.github.io/creator-ghosting-study-1/content-guarantee-terms.html (needs a permanent benable.com URL before launch, see notes).

Contact: Julia. Design + copy source of truth: the card-screen page, https://juliabenable.github.io/creator-ghosting-study-1/screen.html

---

# Benable Content Guarantee Terms

Version 1.0 · Effective [Month DD, 2026]

These are Additional Terms under the [Benable Terms of Use](https://benable.com/terms_of_use_240909.pdf). They apply when you join a brand campaign on Benable that asks you to save a payment card as part of our Content Guarantee. You agree to these terms by saving your card for such a campaign (including when you save it with Apple Pay); the card screen says so where you save it. If anything here conflicts with the Terms of Use, these terms control for the Content Guarantee; everything else in the Terms of Use (including how disputes are resolved) still applies.

## What is the Content Guarantee?

Brands on Benable spend real money providing creators with products and experiences, and Benable guarantees them that every collaboration results in content. Your card on file is what backs that guarantee. Saving a card is required to join campaigns that carry the Content Guarantee, and backing that guarantee is the only thing your card is used for.

## Saving your card

Your card is collected and stored by our payment processor, Stripe. Benable never sees or stores your full card number. Your bank may ask you to verify the card once when you save it; that is normal and only happens at save time. You must be 18 or older to save a card under these terms.

## When can my card be charged?

By agreeing to these terms, you authorize Benable, through its payment processor, to charge your saved card the Guarantee Amount when, and only when, all of the following are true for a campaign:

1. The campaign has closed and you received and kept the product.
2. No content was posted, and no draft was submitted, for that campaign.
3. You have not replied to us on any channel (text, email, or in-app) despite our reasonable attempts to reach you during the campaign.
4. We sent you a final warning naming the amount and the charge date, and that date passed with no post and no reply from you.

Posting your content, or a single reply to the final warning, is enough to stop the charge. Because you approve your card once at save time, a qualifying charge may be processed later without you present.

## How much?

The Guarantee Amount is the brand's product price for your campaign. It is shown in the Content Guarantee details on the screen where you save your card.

- We will never charge more than the amount disclosed to you, and nothing is ever added to it: no fees, no shipping, no taxes, no administrative charges.
- If your product selection changes and the amount goes down, the lower amount applies automatically and we tell you.
- The amount can never go up unless you confirm the new amount first.

## What happens after a charge?

You receive a receipt by email with the full timeline of what happened and how to reach us. If you post your content within 7 days of the charge, we refund it in full. The goal was always the content, not the money.

## When is my card released?

"Released" means a campaign's permission to charge is over: once released, that campaign can never result in a charge. Nothing was ever held or frozen on your account in the meantime (saving a card is not a hold, and no amount is reserved on your statement). Your card is released from a campaign when your post is live and approved, if you leave the campaign before products ship, or if Benable removes the card step from your campaign. Your card itself stays saved for future campaigns unless you replace or remove it.

## Can I replace or remove my card?

You can replace or edit your card on the Payment method page at any time. To remove your only card while it is backing an active campaign, contact us and we will help you. If your card expires or is canceled, no action is needed until you join your next campaign with the Content Guarantee, when we may ask for a current card.

## Changes to these terms

If we change these terms, the new version applies to campaigns you join after the change. A charge is always governed by the version you agreed to when you saved your card for that campaign.

## Questions

Write to kscalzo@benable.com and a real person will answer.

---

## Implementation notes (not part of the terms)

**Consent record.** The card screen's consent line ("By saving your card, you agree to the Content Guarantee Terms") links this page. On save, store: creator id, campaign id, terms version ("1.0"), guarantee amount in cents, timestamp, and the Stripe SetupIntent id. The "Changes to these terms" section makes this load-bearing: any later charge is governed by the stored version and stored amount, never by current values. Same pattern as the ToS-URL-as-version on the users table, but per campaign, and old versions must stay retrievable.

**Stripe.** Save = SetupIntent, usage stays off_session (the default). 3DS at save is handled by the Element; because verification happens at save, the later charge should not need it. Charge = PaymentIntent with off_session true and confirm true, amount = the stored amount.

**Amount ratchet.** Product selection changes that lower the amount: update the stored amount and notify the creator. Any increase requires the creator to re-confirm (a new consent record). A charge above the stored disclosed amount must be impossible at the API level, not just policy.

**Charges are manual only.** No cron ever charges a card. The four conditions in "When can my card be charged?" map to Katie's charge-console checklist: delivery means courier-confirmed (not shipped); "no content" includes drafts in review (a draft voids the charge); "no reply" counts every channel including Instagram DMs; the final warning names amount + date, and the date must land on a weekday outside holiday windows. Charge fires only when all rows are green, second-admin confirm for the first ten.

**Refund path.** Post within 7 days of a charge = full refund to the same card. Do not promise a refund timeline anywhere in comms (Julia, Aug 20). The receipt email carries the timeline of facts and the refund door.

**Release events.** Three events end a campaign's permission to charge, irreversibly: post live and approved, creator leaves before products ship, or the card step is waived. Each release should also trigger the "your card was never charged" message; that line is the trust engine for campaign #2.

**Hosting + versioning.** This page needs a permanent URL (benable.com/content-guarantee-terms or similar) before launch; the consent line must link it. Keep each published version addressable (v1.0, v1.1, ...) since consent records reference the version accepted.

**Settings surface.** The Payment method page (creator settings) shows the explainer, the saved card with Edit, and Add payment method — deliberately no amounts and no campaign linkage. Edit's Replace runs a fresh SetupIntent behind the same screen and re-binds the active campaign's consent record to the new card; removing the only card while it backs an active campaign routes to support (always granted, logged). If multiple cards exist, each campaign's consent record binds to exactly one card at save; nothing silently switches which card a Guarantee can charge.

**Open items:**
1. Permanent hosting URL for this page (consent line must link it; keep each version addressable).
2. Legal pass: with Julia — wiring can proceed, publication waits on her go.
