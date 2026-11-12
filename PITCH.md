# PITCH.md

Six lines and a lever. Your words. The last two are scored.

Built: A disruption-care agent for Larkspur with the nine given tools plus next_available_day and fare_rules, which we first wrote in agent.py and then moved onto the MCP server. We also fixed the loop (it dropped the tool_use block), the date format on get_flight_status, and the one-word search_alternatives description.
Does: For a disrupted flight it finds the booking, checks the flight's status, checks what Larkspur owes, searches replacement flights, holds a seat, and quotes the Handbook when a customer asks why something is or is not covered.
Number: 3,041 to 3,031 tokens in per API turn, for the tool definitions alone (11 tools), after moving the two tools from agent.py to the MCP server.
Safety check: It holds a seat but never books it. On J5NU8S the customer said "yes, go ahead and confirm it", the agent called hold_seat (HOLD-16E553) and stopped, and told them only their own confirm click can finish it. confirm_rebooking was never called in that trace.
Next: Make next_available_day count the whole party, then run a two-passenger case and read the trace to check the date it gives.
Still broken: next_available_day answers for one passenger. J5NU8S has two, and the tool reported a party of one, so its date may be too early for a group. We saw it answer for one passenger but have not found a case where the date is actually wrong.
Lever: <cost | speed | intelligence>

## Priya asked

Costs:
Wrong:
Runs it:
Left out:
