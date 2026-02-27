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

# Alumni Sorting - Task 2 Learnings

## Month Abbreviation Mapping Strategy
### Key Insights
- **Month format consistency**: All abbreviations use period (.) except May in some cases
- **Data analysis**: Found actual usage patterns in member data vs theoretical requirements
- **Template function design**: Reusable partial for month-to-number conversion
- **Sortable date format**: YYYYMM provides proper chronological ordering

### Implementation Patterns
1. **Dictionary-based mapping**: Efficient lookup using Hugo's dict function
2. **Partial function design**: Clean separation of concerns with reusable templates
3. **Fallback handling**: Graceful degradation for invalid months (returns 0)
4. **Integration approach**: Enhanced existing sorting logic without breaking changes

### Technical Discoveries
- **Member data usage**: Only 8 of 12 months actually used in current data
- **Most common**: Sep. appears most frequently in member summaries
- **Existing patterns**: All found month abbreviations follow consistent format with periods
- **Template compatibility**: Works seamlessly with existing Hugo template functions

## Hugo Template Techniques
### Advanced Partial Functions
- **Parameter passing**: Direct string parameter to partial function
- **Return value handling**: Using `return` for early exit from loops
- **Template comments**: Comprehensive documentation for future maintenance

### String Manipulation
- **Prefix/suffix removal**: Using `strings.TrimPrefix`, `strings.Before`, `strings.After`
- **Format conversion**: `printf` for creating sortable date strings
- **Type conversion**: `(int $year)` for numeric operations

### Sorting Enhancements
- **Multi-criteria sorting**: Separate sort operations for date and title
- **Custom data structures**: Using `dict` to create sortable objects
- **Future-proofing**: Using large numbers for current/missing dates

## Best Practices Established
1. **Testing-driven approach**: Created test scripts before implementation
2. **Evidence documentation**: Comprehensive verification results
3. **Backward compatibility**: Enhanced existing functionality without breaking changes
4. **Error handling**: Robust fallback for edge cases

## Performance Optimizations
- **Reduced regex calls**: Single regex call with result storage
- **Efficient sorting**: Numeric YYYYMM format vs string comparison
- **Memory efficient**: Temporary data structures only during processing

## Future Enhancements
- **Date range support**: Handle full date ranges like "Sep. 2024 ~ Jun. 2025"
- **Internationalization**: Support for non-English month abbreviations
- **Dynamic regex**: Adaptive pattern based on actual data format
- **Cache layer**: Memoization for frequently accessed month mappings

## Integration Success Factors
- **Minimal changes**: Only modified necessary sections in portfolio.html
- **Clear separation**: Month mapping logic isolated in dedicated partial
- **Testing coverage**: Comprehensive test and verification scripts
- **Documentation**: Detailed implementation documentation for maintenance