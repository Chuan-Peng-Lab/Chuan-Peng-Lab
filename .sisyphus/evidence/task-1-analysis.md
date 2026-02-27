# Task 1 Analysis: Template Implementation Issues

## Template File: `layouts/partials/widgets/portfolio.html`

## Current Sorting Flow Analysis

### Default Sorting (Lines 87-91)
```hugo
{{/* Sort */}}
{{ $sort_by := $st.Params.content.sort_by | default "Date" }}
{{ $sort_ascending := $st.Params.content.sort_ascending | default (eq $st.Params.content.order "asc") | default false }}
{{ $sort_order := cond $sort_ascending "asc" "desc" }}
{{ $query = sort $query $sort_by $sort_order }}
```

**Current Approach:**
- Uses Hugo's built-in `sort` function
- Sorts by `Date` (or custom field) in ascending/descending order
- This happens BEFORE custom alumni sorting

### Custom Alumni Sorting (Lines 93-132)
```hugo
{{/* Custom sorting for alumni by departure date */}}
{{ $alumni := slice }}
{{ $non_alumni := slice }}

{{ range $query }}
  {{ if in .Params.tags "Alumni" }}
    {{ $alumni = $alumni | append . }}
  {{ else }}
    {{ $non_alumni = $non_alumni | append . }}
  {{ end }}
{{ end }}

{{/* Sort alumni by departure date (extracted from summary) */}}
{{ $sorted_alumni := slice }}
{{ range $alumni }}
  {{ $end_date := "" }}
  {{ $summary := .Params.summary }}
  {{/* Parse "~ Jun. 2025" or "~ Now" from summary */}}
  {{ if findRE "~\\s*([A-Za-z]+\\.\\s*\\d{4})" $summary }}
    {{ $matches := findRE "~\\s*([A-Za-z]+\\.\\s*\\d{4})" $summary }}
    {{ $end_date = index $matches 0 }}
  {{ else if findRE "~\\s*Now" $summary }}
    {{ $end_date = "~ Now" }}
  {{ end }}
  {{ $sorted_alumni = $sorted_alumni | append (dict "page" . "end_date" $end_date "title" .Title) }}
{{ end }}

{{/* Sort by end_date desc, then by title asc */}}
{{ $sorted_alumni = sort $sorted_alumni "end_date" "desc" }}
{{ $sorted_alumni = sort $sorted_alumni "title" "asc" }}

{{/* Combine: alumni first, then non-alumni */}}
{{ $query = slice }}
{{ range $sorted_alumni }}
  {{ $query = $query | append .page }}
{{ end }}
{{ range $non_alumni }}
  {{ $query = $query | append . }}
{{ end }}
```

## Identified Issues

### 1. Date Parsing Issue (Lines 111-116)
**Issue:** Date regex pattern is incomplete
- Current pattern: `"~\\s*([A-Za-z]+\\.\\s*\\d{4})"`
- Matches: `"~ Sep. 2025"` but fails on `"~ Sep. 2025 ~ Jun. 2025"`
- Missing case: date ranges like `"~ Sep. 2024 ~ Jun. 2025"`

**Impact:** Alumni with date ranges won't be parsed correctly, leading to empty `end_date` values and incorrect sorting.

### 2. Date Format Inconsistency
**Issue:** Date format normalization missing
- Parse dates like `"~ Sep. 2025"` and `"~ Now"`
- No consistent format for comparison
- `sort` function will treat strings differently than dates

**Impact:** Sorting becomes unreliable as `"~ Now"` vs `"~ Sep. 2025"` string comparison may not work as expected.

### 3. Fallback Logic Missing (Line 116)
**Issue:** No handling for alumni without end dates
- Current code only handles two cases: date pattern or "Now"
- Alumni with different summary formats get empty `end_date`
- These alumni get sorted incorrectly (empty strings first in descending order)

**Impact:** Alumni without properly formatted dates will appear at the end of the list instead of being handled properly.

### 4. Multiple Sort Operations (Lines 121-122)
**Issue:** Multiple sort operations without stable sorting
- First sort by `end_date desc`
- Second sort by `title asc`
- This doesn't guarantee stable sorting for entries with same `end_date`

**Impact:** Alumni with same departure date may not maintain alphabetical order consistently.

### 5. Regex Performance Issue (Lines 111-112)
**Issue:** Redundant regex operations
- `findRE` called twice for the same pattern on each alumni summary
- Inefficient for large datasets

**Impact:** Unnecessary performance overhead when processing many alumni records.

### 6. Date Comparison Logic Missing
**Issue:** No actual date comparison, just string sorting
- Dates like `"~ Sep. 2025"` sorted as strings rather than actual dates
- `"~ Now"` should always be latest, but may not work correctly

**Impact:** Chronological sorting may not be accurate, especially for mixed date formats.

### 7. Edge Case: PI Member (hcp)
**Issue:** PI member has no date in summary
- From context: "Only PI (hcp) has no date in summary field"
- Current code doesn't handle this special case

**Impact:** PI member might be sorted incorrectly or excluded from proper ordering.

### 8. Template Logic Flow Issue
**Issue:** Default sorting happens before custom alumni sorting
- Line 91: Default sorting applied to entire query
- Lines 93-132: Custom sorting applied to alumni only
- This means default sorting is effectively overridden but still executed

**Impact:** Performance waste on unnecessary default sorting that gets replaced.

## Current vs Expected Behavior

**Expected Behavior:**
- Alumni should be sorted by departure date (most recent first)
- Within same departure date, sort alphabetically by name
- Non-alumni should retain default sorting behavior
- PI should appear at the end or in appropriate position

**Current Behavior:**
- Alumni date parsing is incomplete
- Multiple sorting issues cause incorrect ordering
- Performance issues with redundant regex operations

## Recommended Fixes

1. **Update date parsing regex** to handle date ranges
2. **Add date normalization** for consistent comparison
3. **Implement fallback logic** for alumni without dates
4. **Use stable sorting** for multiple sort criteria
5. **Optimize regex operations** by caching results
6. **Handle PI member special case**
7. **Reorder sorting logic** for better performance

## Evidence
- Template lines 87-132 contain current sorting implementation
- Date regex patterns identified in lines 111-116
- Multiple sort operations in lines 121-122
- Context indicates PI (hcp) has no date in summary field