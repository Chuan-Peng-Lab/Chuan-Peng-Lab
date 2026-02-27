# Alumni Sorting - Task 1 Learnings

## Pattern Recognition
### Template Structure Analysis
- Hugo template follows logical separation: default sorting → alumni filtering → custom sorting → reassembly
- Uses slice operations effectively for data transformation
- Implements tag-based filtering (`in .Params.tags "Alumni"`) for member categorization

### Date Parsing Challenges
- **Current regex pattern**: `"~\\s*([A-Za-z]+\\.\\s*\\d{4})"` works for single dates but fails on date ranges
- **Real-world format**: Chinese summaries use `__Sep. 2024 ~ Jun. 2025__` format
- **Key insight**: Date parsing needs to handle both English and Chinese formats, single dates and ranges

### Sorting Logic Patterns
- **Multi-step sorting**: First by date (descending), then by title (ascending)
- **Template merging**: Uses `dict` to create temporary structures for sorting, then reassembles final query
- **Performance consideration**: Multiple `sort` operations should be optimized into single stable sort

## Successful Approaches
1. **Tag-based separation**: Clean separation of alumni vs non-alumni using Hugo's `in` function
2. **Dictionary transformation**: Creating temporary dict structures for complex sorting criteria
3. **Logical reassembly**: Clear separation of alumni (sorted) and non-alumni (original order) in final output

## Problems Identified
1. **Regex pattern limitation**: Current pattern doesn't handle date ranges
2. **String vs date comparison**: Treating dates as strings instead of actual date objects
3. **Missing fallback logic**: No handling for alumni without proper date formatting
4. **Performance waste**: Redundant regex calls and unnecessary default sorting

## Technical Conventions
- **Hugo template functions**: Uses `findRE`, `sort`, `cond`, `slice`, `append`, `dict` effectively
- **Template variable naming**: Clear prefixes (`$alumni`, `$non_alumni`, `$sorted_alumni`)
- **Comment structure**: Well-documented sections with clear purpose descriptions

## Optimization Opportunities
1. **Cache regex results**: Call `findRE` once and store results
2. **Date normalization**: Convert parsed dates to comparable format
3. **Stable sorting**: Use Hugo's stable sort for multiple criteria
4. **Conditional fallback**: Handle edge cases like PI member with no date

## File Structure Insights
- Template override path: `layouts/partials/widgets/portfolio.html`
- Separates sorting logic from display logic effectively
- Maintains compatibility with existing isotope/masonry views

## Next Task Considerations
- Build on existing template structure
- Improve date parsing robustness
- Implement proper date comparison logic
- Add comprehensive error handling
- Optimize performance by eliminating redundant operations