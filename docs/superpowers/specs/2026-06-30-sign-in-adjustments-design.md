# Sign-in Page Adjustments — Design

Date: 2026-06-30
Scope: `web/default` frontend only (no backend changes)

## Goal

Adjust the `/sign-in` page for the Wanda deployment:
1. Remove the "Don't have an account? Sign up" entry (registration closed) — **already done** by disabling registration; no code change needed here.
2. Rename the username field label and placeholder to "万达工号" (Wanda Employee ID).
3. Add a clickable "说明文档" (documentation) link on the right side of the sign-in title row, opening the model-access guide in a new window.

## Out of scope

- Backend, auth logic, form schema, and the `username` submission field name are untouched.
- The public `AuthLayout` is shared by other auth pages (sign-up, forgot-password, otp, oauth-callback) and is NOT modified — the doc link lives only in the sign-in page.
- The registration entry in `sign-in/index.tsx` is left as-is (it already doesn't render when registration is disabled).

## Files

- `web/default/src/features/auth/sign-in/components/user-auth-form.tsx` — field label + placeholder
- `web/default/src/features/auth/sign-in/index.tsx` — title-row doc link
- `web/default/src/i18n/locales/{en,zh,ja,fr,ru,vi}.json` — new i18n keys

## Change 1 — Field label & placeholder → Wanda Employee ID

In `user-auth-form.tsx`:

- Label: `t('Username or Email')` → `t('Wanda Employee ID')`
- Placeholder: `t('Enter your username or email')` → `t('Enter your Wanda employee ID')`

No change to the `loginFormSchema`, the form's `username` field, or the `login({ username, ... })` submission. The underlying field name stays `username`.

New i18n entries (added to all six locale files):

| key | en | zh | ja | fr | ru | vi |
|---|---|---|---|---|---|---|
| `Wanda Employee ID` | Wanda Employee ID | 万达工号 | ワンダ社員番号 | Identifiant Wanda | Табельный номер Wanda | Mã nhân viên Wanda |
| `Enter your Wanda employee ID` | Enter your Wanda employee ID | 请输入万达工号 | ワンダ社員番号を入力 | Saisir votre identifiant Wanda | Введите табельный номер Wanda | Nhập mã nhân viên Wanda |

The old keys (`Username or Email`, `Enter your username or email`) are left in place — they are not referenced after this change but removing them is out of scope and risks other usages.

## Change 2 — "说明文档" doc link in the title row

In `sign-in/index.tsx`, replace the current `<h2>` title block:

```tsx
<h2 className='text-center text-2xl font-semibold tracking-tight sm:text-left'>
  {t('Sign in')}
</h2>
```

with a two-column flex row (space-between, items-baseline): title on the left, doc link on the right, both aligned on the same row at the top.

The doc link:

- Text: `t('Documentation')` (zh = 说明文档), prefixed with a `BookOpen` / `ExternalLink` lucide icon.
- Styled clearly as clickable: primary color, underline, underline-offset, hover opacity.
- Opens `http://10.1.50.87:5173/guide/model-access.html` in a new window via an `<a>` with `target='_blank' rel='noopener noreferrer'`.
- The URL is defined as a module-level constant at the top of the file (`const DOC_URL = '...'`) so it's easy to change later.

New i18n key `Documentation`:

| key | en | zh | ja | fr | ru | vi |
|---|---|---|---|---|---|---|
| `Documentation` | Documentation | 说明文档 | 説明ドキュメント | Documentation | Документация | Tài liệu |

The title stays `t('Sign in')` (already translated). The existing register-entry `<p>` block below the title is left untouched (it doesn't render when registration is disabled).

## Responsive note

On narrow screens the row uses `justify-between items-baseline`; the link text is short enough that it won't collide with the title. The existing `sm:text-left` responsive alignment on the title is preserved on its own element.

## Testing / verification

1. Start the frontend dev server (`bun run dev` in `web/default/`).
2. Open `/sign-in` and confirm:
   - The username field label and placeholder show "万达工号" / "请输入万达工号".
   - The title row shows "登录" on the left and a clickable "说明文档" link on the right, vertically aligned.
   - Clicking "说明文档" opens the model-access guide in a new browser tab.
3. Switch the UI language to English (and one other) and confirm the field label, placeholder, and doc link render translated.
