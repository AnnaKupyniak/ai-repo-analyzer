# PR LLM Evaluation

## Goal

Submit the collected file patches to the LLM to perform an automated code review on the code changes.

## Evaluation Criteria

The LLM must evaluate the diffs according to the following criteria:

1. **Bugs & Reliability**: Identify potential logic flaws, null pointer exceptions, unhandled errors, or edge cases.
2. **Code Style & Readability**: Review naming conventions, structure, and adherence to clean code principles.
3. **Performance & Optimization**: Identify inefficient algorithms, redundant operations, or database/network bottlenecks.
4. **Edge Cases**: Check how the new code handles exceptional circumstances or unexpected inputs.
5. **Positive Reinforcement**: Acknowledge clean, well-architected, and correct changes.

## Prompt Construction

For each file in the Pull Request:
- Provide the file name and the patch (diff).
- Instruct the LLM to write specific recommendations pointing to the line numbers/ranges in the patch.
- Request the final output formatted in clean Markdown.
