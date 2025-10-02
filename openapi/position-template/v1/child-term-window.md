# Child Term Window Endpoint

## Purpose
When editing an existing position template that already has children, we must keep the parent-child timeline relationship intact. This endpoint exposes the valid start and end term window the UI should allow for that position.

## Path
`GET /api/v1/position-templates/{positionId}/children/allowed-terms`

## Response Overview
- `position`: metadata about the position being edited, including whether it has a parent or children.
- `allowedStart`: lower and upper bounds (`min`, `max`) for the editable start term.
- `allowedEnd`: lower and upper bounds for the editable end term.
- `constraintSources`: echoes the effective ranges contributed by the parent and children so the client can explain validation decisions.
- `displayFormat`: indicates how dates should be rendered (for reuse alongside existing parent term UX).

All dates use the `YYYY-MM-DD` format, matching the rest of the API.

## Validation Rules Encoded by the Endpoint
1. **Parent constraint**: if the position has a parent, its start term cannot be earlier than the parent's start, and its end term cannot extend beyond the parent's end.
2. **Child constraint**: if the position has children, the start term cannot move later than the earliest child start, and the end term cannot move earlier than the latest child end.
3. **Open-ended ranges**: `null` values mean "no boundary". The service preserves open ranges whenever no constraint exists.
4. **Start ≤ End**: when both bounds exist, `min` is always less than or equal to `max`.

## Example
```json
{
  "data": {
    "position": {
      "id": "7d057fa3-76df-4ddc-b9f2-dbdacb82f2f1",
      "type": "TITLE",
      "displayName": "Position A",
      "startTerm": "2025-04-01",
      "endTerm": "2026-03-31",
      "hasParent": true,
      "hasChildren": true
    },
    "allowedStart": {
      "min": "2025-01-01",
      "max": "2025-06-01"
    },
    "allowedEnd": {
      "min": "2025-09-30",
      "max": "2026-03-31"
    },
    "constraintSources": {
      "parent": {
        "min": "2025-01-01",
        "max": "2026-03-31"
      },
      "children": {
        "min": "2025-06-01",
        "max": "2025-09-30"
      }
    },
    "displayFormat": {
      "pattern": "DD/MM/YYYY"
    }
  },
  "message": "fetch successfully"
}
```

## Usage Notes
- Call this endpoint before opening the date picker for a position that already has children. Reuse the `allowedStart` and `allowedEnd` ranges to clamp the UI controls.
- If either `min` or `max` is `null`, the UI should treat that side of the range as open.
- Combine this result with the existing parent-term endpoint to validate both directions of the hierarchy when re-parenting positions.
