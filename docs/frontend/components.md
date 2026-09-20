# Components

Primary source: `apps/web/` directory listing.

| File | Responsibility |
| --- | --- |
| `app.js` | Shell — session, navigation, patient selection |
| `clinical-workflows.js` | Encounter/note/observation/condition/allergy/task commands |
| `care-team.js` | Arbetslista (scheduling/check-in) |
| `draft-editor.js` | Note draft editing with autosave — see [forms.md](forms.md) |
| `diagnosis-picker.js` | ICD-10-SE search/selection UI |
| `access-workspace.js` | Workforce administration and audit review |
| `coordination-workspace.js` | Eir Samverkan case/SIP/messages UI |
| `deterioration-workspace.js` | Monitoring enrollment, alerts, response |
| `follow-up-workspace.js` | Bevakning — follow-up oversight |
| `integration-workspace.js` | Lab-integration operations view |
| `module-workspace.js` | Per-unit optional-module activation |
| `renderers/timeline.js`, `renderers/table.js`, `renderers/shared.js` | Pluggable chart renderers — see [../architecture/frontend.md](../architecture/frontend.md) |
| `style.css` | Styling |
| `guide.html`, `index.html` | Entry pages |
| `social/` | Static Open Graph/X preview image ([docs/SHARING.md](../SHARING.md)) |

Each workspace module talks directly to the authenticated JSON API — there
is no intermediate frontend state-management framework or client-side
store; component state is local to each module.
