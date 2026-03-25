# Vite Modularization Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development to implement this plan.

**Goal:** Split the 6,600-line monolith `src/index.html` into modular JSX files under `vite-src/` with proper imports/exports, maintaining identical functionality.

**Architecture:** Extract each logical section of the monolith into its own file. Keep React hooks + useLS state management (no Zustand). App.jsx remains the state orchestrator but imports all components/screens. Vite builds to single HTML via vite-plugin-singlefile for StatiCrypt encryption.

**Tech Stack:** React 18, Vite, Tailwind CSS 4, @supabase/supabase-js, qrcode-generator, vite-plugin-singlefile, staticrypt

---

## File Structure

```
vite-src/
├── main.jsx                         # Entry point (ReactDOM.createRoot)
├── index.html                       # Root HTML (CDN scripts removed, Vite handles)
├── index.css                        # All CSS (extracted from <style> block)
├── App.jsx                          # Main component: state, sync, routing, nav
├── constants.js                     # USERS, TEMPS, DEFAULT_CATS, STOCK_SOURCES, etc.
├── utils/
│   ├── dates.js                     # normDate, toDate, addDays, fmt, fmtFull, isPast, isToday, dFromNow
│   ├── helpers.js                   # uid, isTouchDevice, dlICS, hashPin
│   ├── stocks.js                    # guessSource, stockUrl, parseJaneliaLine, fetchBDSCInfo, stockTags, BALANCER_MARKERS, getScreeningGuide
│   ├── crosses.js                   # STATUSES, STATUS_SHORT, nextSt, stIdx, crossDetect, getNextAction, crossLabelText
│   ├── vcs.js                       # VCS_DEFAULTS, computeNextActions, getVcsStatus, vcsWindowProgress, fmtDur, fmtTime, getFlipDays, calcTL, getTL
│   ├── supabase.js                  # getSb, resetSb, field maps, supabasePull/Push/SyncDeletes, virgin/exp/transfer sync, mergeStocks/mergeCrosses, deletion tracking
│   └── demo.js                      # makeDemoData
├── hooks/
│   ├── useLS.js                     # localStorage-backed useState
│   ├── useToast.js                  # Toast state management (add, rm)
│   └── useSupabaseRealtime.js       # Realtime subscriptions to all Supabase tables
├── components/
│   ├── ui/
│   │   ├── Modal.jsx
│   │   ├── Confirm.jsx
│   │   ├── Btn.jsx
│   │   ├── Inp.jsx
│   │   ├── Txt.jsx
│   │   ├── Field.jsx
│   │   ├── Toasts.jsx
│   │   ├── CircleProgress.jsx
│   │   ├── ErrorBoundary.jsx
│   │   └── index.js                 # Barrel export
│   ├── TagBadges.jsx
│   ├── CrossTimeline.jsx
│   ├── CrossCard.jsx
│   ├── EditCrossModal.jsx
│   ├── NewCrossWizard.jsx
│   ├── StockModal.jsx
│   ├── VcsSetup.jsx
│   ├── PinLock.jsx
│   ├── PrintLabelsModal.jsx
│   ├── VirginNotifSettings.jsx
│   ├── BackgroundCanvas.jsx
│   ├── AmbientFly.jsx
│   └── Icons.jsx
├── screens/
│   ├── HomeScreen.jsx
│   ├── StocksScreen.jsx
│   ├── VirginsScreen.jsx
│   ├── ExpScreen.jsx
│   └── SettingsScreen.jsx
```

---

## Chunk 1: Foundation (Constants, Utils, Hooks)

### Task 1: CSS extraction
**Files:** Create `vite-src/index.css`
- [ ] Extract lines 14-392 (all CSS inside `<style>` tags) from monolith
- [ ] Add Tailwind v4 import at top: `@import "tailwindcss";`
- [ ] Commit

### Task 2: Constants
**Files:** Create `vite-src/constants.js`
- [ ] Extract: TEMPS, DEFAULT_CATS, USERS, STOCK_SOURCES, STOCK_VARIANTS, LABEL_FORMATS, BG_TYPES, BG_LABELS, TAG_STYLE
- [ ] Export all as named exports
- [ ] Commit

### Task 3: Date utilities
**Files:** Create `vite-src/utils/dates.js`
- [ ] Extract: normDate, toDate, addDays, fmt, fmtFull, isPast, isToday, dFromNow, today
- [ ] Export all as named exports
- [ ] Commit

### Task 4: Helper utilities
**Files:** Create `vite-src/utils/helpers.js`
- [ ] Extract: uid, isTouchDevice, dlICS, hashPin, sn, sg, cl, clFull
- [ ] Import from dates.js as needed
- [ ] Commit

### Task 5: Stock utilities
**Files:** Create `vite-src/utils/stocks.js`
- [ ] Extract: guessSource, stockUrl, parseJaneliaLine, janeliaUrl, fetchBDSCInfo, parseFlyBase, flybaseUrl, stockTags, BALANCER_MARKERS, getScreeningGuide, is18, tempLabel, tempFull, detectOC
- [ ] Import from constants.js, dates.js as needed
- [ ] Commit

### Task 6: Cross utilities
**Files:** Create `vite-src/utils/crosses.js`
- [ ] Extract: STATUSES, STATUS_SHORT, nextSt, stIdx, crossDetect, getNextAction, crossLabelText
- [ ] Import from stocks.js, helpers.js as needed
- [ ] Commit

### Task 7: VCS utilities
**Files:** Create `vite-src/utils/vcs.js`
- [ ] Extract: VCS_DEFAULTS, vcsKey, getVirginWindowH, makeVcs, parseHHMM, fmtHHMM, fmtDur, computeDeadline, computeNextActions, getVcsStatus, vcsWindowProgress, fmtTime, getFlipDays, calcTL, getTL
- [ ] Import from dates.js as needed
- [ ] Commit

### Task 8: Supabase utilities
**Files:** Create `vite-src/utils/supabase.js`
- [ ] Extract: SUPABASE_URL, SUPABASE_KEY, STOCK_FIELD_MAP, CROSS_FIELD_MAP, toSnake, toCamel, getSb, resetSb, supabasePull, supabasePush, supabaseSyncDeletes, supabasePushVirginBank, supabasePullVirginBank, supabasePushExpBank, supabasePullExpBank, supabasePushTransfers, supabasePullTransfers, mergeStocks, mergeCrosses, markDeleted, isDeletedLocally, markEdited, isEditedLocally, supabaseDeleteNow
- [ ] Commit

### Task 9: Demo data
**Files:** Create `vite-src/utils/demo.js`
- [ ] Extract: makeDemoData function
- [ ] Import from constants.js, dates.js, vcs.js as needed
- [ ] Commit

### Task 10: Hooks
**Files:** Create `vite-src/hooks/useLS.js`, `useToast.js`, `useSupabaseRealtime.js`
- [ ] Extract useLS hook
- [ ] Extract useToast hook + Toasts component (keep Toasts with hook, re-export from ui/)
- [ ] Extract useSupabaseRealtime hook
- [ ] Import from supabase.js as needed
- [ ] Commit

---

## Chunk 2: UI Primitives and Small Components

### Task 11: UI primitives
**Files:** Create all files in `vite-src/components/ui/`
- [ ] Modal.jsx, Confirm.jsx, Btn.jsx, Inp.jsx, Txt.jsx, Field.jsx, CircleProgress.jsx, ErrorBoundary.jsx
- [ ] Create index.js barrel export
- [ ] Commit

### Task 12: Small components
**Files:** Create TagBadges.jsx, CrossTimeline.jsx, Icons.jsx, VirginNotifSettings.jsx
- [ ] Extract each with proper imports
- [ ] Commit

---

## Chunk 3: Major Components

### Task 13: CrossCard
**Files:** Create `vite-src/components/CrossCard.jsx`
- [ ] Extract CrossCard (~400 lines)
- [ ] Import from ui/, utils/, hooks/
- [ ] Commit

### Task 14: EditCrossModal
**Files:** Create `vite-src/components/EditCrossModal.jsx`
- [ ] Extract EditCrossModal
- [ ] Commit

### Task 15: NewCrossWizard
**Files:** Create `vite-src/components/NewCrossWizard.jsx`
- [ ] Extract NewCrossWizard
- [ ] Commit

### Task 16: StockModal + VcsSetup
**Files:** Create `vite-src/components/StockModal.jsx`, `VcsSetup.jsx`
- [ ] Extract both components
- [ ] VcsSetup imported by StockModal
- [ ] Commit

### Task 17: PinLock
**Files:** Create `vite-src/components/PinLock.jsx`
- [ ] Extract PinLock with hashPin import from helpers.js
- [ ] Commit

### Task 18: PrintLabelsModal
**Files:** Create `vite-src/components/PrintLabelsModal.jsx`
- [ ] Extract PrintLabelsModal + crossLabelText (or import from crosses.js)
- [ ] Commit

### Task 19: BackgroundCanvas + AmbientFly
**Files:** Create `vite-src/components/BackgroundCanvas.jsx`, `AmbientFly.jsx`
- [ ] Extract both visual components
- [ ] Commit

---

## Chunk 4: Screens and App

### Task 20: HomeScreen
**Files:** Create `vite-src/screens/HomeScreen.jsx`
- [ ] Extract HomeScreen (~830 lines)
- [ ] Import CrossCard, VcsSetup, CrossTimeline, etc.
- [ ] Commit

### Task 21: StocksScreen
**Files:** Create `vite-src/screens/StocksScreen.jsx`
- [ ] Extract StocksScreen (~640 lines)
- [ ] Commit

### Task 22: Remaining screens
**Files:** Create VirginsScreen.jsx, ExpScreen.jsx, SettingsScreen.jsx
- [ ] Extract all three
- [ ] Commit

### Task 23: App.jsx
**Files:** Create `vite-src/App.jsx`
- [ ] Extract App component with all state, sync effects, routing, nav
- [ ] Import all screens, components, hooks
- [ ] Commit

### Task 24: Entry point and HTML
**Files:** Create/update `vite-src/main.jsx`, `vite-src/index.html`
- [ ] main.jsx: ReactDOM.createRoot + render App
- [ ] index.html: minimal HTML shell (no CDN scripts, Vite handles bundling)
- [ ] Commit

---

## Chunk 5: Build Pipeline

### Task 25: Vite config and dependencies
**Files:** Update `vite.config.js`, `package.json`
- [ ] Ensure vite-plugin-singlefile is installed
- [ ] Configure build to output single HTML to dist/
- [ ] Add encrypt script: `vite build && staticrypt dist/index.html ...`
- [ ] Commit

### Task 26: Build and verify
- [ ] Run `npm run build` — verify no import errors
- [ ] Run `npm run dev` — verify app loads and functions
- [ ] Test: stocks CRUD, cross workflow, virgin bank, exp bank, sync
- [ ] Commit final fixes
