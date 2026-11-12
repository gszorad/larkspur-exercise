# PITCH.md

Six lines and a lever. Your words. The last two are scored.

Built: A disruption-care agent for Larkspur with the nine given tools plus next_available_day and fare_rules, which we first wrote in agent.py and then moved onto the MCP server. We also fixed the loop (it dropped the tool_use block), the date format on get_flight_status, and the one-word search_alternatives description.
Does: For a disrupted flight it finds the booking, checks the flight's status, checks what Larkspur owes, searches replacement flights, holds a seat, and quotes the Handbook when a customer asks why something is or is not covered.
Number: 3,041 to 3,031 tokens in per API turn, for the tool definitions alone (11 tools), after moving the two tools from agent.py to the MCP server.
Safety check: It holds a seat but never books it. On J5NU8S the customer said "yes, go ahead and confirm it", the agent called hold_seat (HOLD-16E553) and stopped, and told them only their own confirm click can finish it. confirm_rebooking was never called in that trace.
Next: Make next_available_day count the whole party, then run a two-passenger case and read the trace to check the date it gives.
Still broken: next_available_day answers for one passenger. J5NU8S has two, and the tool reported a party of one, so its date may be too early for a group. We saw it answer for one passenger but have not found a case where the date is actually wrong.
Lever: intelligence

## Priya asked

Costs: 18,552 tokens in and 1,042 out per contact, about 12.5 seconds at the median, on the usual five tickets with claude-opus-5-5 (3 runs each). At Opus 5.5 rates ($4 in, $20 out per million tokens) that is about $0.095 per contact against $6.90 for a person. Over about 307,000 contacts a year that is roughly $29,200 a year against about $2,118,000 for people. It is model cost only, not the loaded cost, and we worked it out from the saved token counts: the saved bench files still show $0.0713 because they were run before the Opus price was added to bench.py. Moving from Sonnet 5 with no tone instruction to Opus 5.5 with it raised tokens out by 66% and median time by 32%.
Wrong: We have seen it be wrong in our own runs: once it asked a customer for a flight number it could have looked up, and once it said a "refunds team will follow up" without saying a person does refunds. We find out by running the six evals in evals/cases.json; four are hard gates, and a failure blocks the release. When it is wrong on a refund, a group, a minor or a legal threat, the agent is told to hand over to a person with escalate_to_human. We did not build anything that watches live customers, so today a wrong answer is only caught by an eval someone ran.
Runs it: Nobody is named yet and we have not decided. What a maintainer would have: run.py --trace to read one conversation, eval_harness.py for the six cases, and bench.py before and after any change. Someone at Larkspur has to own those commands, and the evals, the tone instruction and the model choice need an owner.
Left out: Refunds, groups, unaccompanied minors, partner flights and other-airline rebooking go to a person, because the Handbook says people handle them. It never finalizes a rebooking without the customer's own confirm click, by design. We left out party size in next_available_day, languages other than English, and any live monitoring of real conversations, for lack of time.
