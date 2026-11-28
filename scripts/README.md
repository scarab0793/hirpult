# Hírpult – hírgyűjtés és szerkesztés

A GitHub Actions naponta négy alkalommal (6, 10, 13, 16 óra, UK idő) gyűjt RSS/Atom híreket a 119 forrásból. A sikeres és sikertelen források listája a data/news.json fájlban található. A data/news-history.json a nyers hírarchívum.

A cikkírást a workflow a Claude API-val végzi (`scripts/enrich_news.py`, titok: `ANTHROPIC_API_KEY`): a friss RSS-hírekhez letölti a forráscikket, eredeti magyar cikket ír belőle (szubjektív nézőponttal és „Amit érdemes tudni” dobozzal), kódban ellenőrzi (számok, idézetek, szó szerinti átfedés, hossz), és a data/editorial.json-ba írja. Beállítások (repo Variables, nem titok): `HIRPULT_AI_MAX_ARTICLES` (futásonként, alap 15, max 25), `HIRPULT_AI_MAX_CALLS` (alap 45), `HIRPULT_AI_MODEL`, `HIRPULT_AI_EFFORT`. Kulcs nélkül a futás csak RSS-t gyűjt. Futások: hétköznap 6–20 óra között kétóránként, hétvégén 6, 9, 12, 15, 18 (UK idő). A tech forrásokból írt cikkek `c: "Tech"`, a megyeiek `r: "Megyei"` és `m` mezőt kapnak. A backfill szkript (`backfill_news.py`) kézzel indítható, a workflow nem futtatja. A duplikációszűrés a cikkírás után fut.

## Szerkesztőségi publikálás
- A nyers RSS nem kész cikk. Ellenőrizni kell az eredeti forrásokat, lehetőleg több független forrásból.
- A data/editorial.json meglévő items tömbjét kell bővíteni a jelenlegi séma szerint, egyedi id, published_at, ai_edited: true, t, s, p, src, r, c, st, time, w, cav, op mezőkkel.
- Megyei cikk esetén m mezőben a megye neve szerepeljen; sport esetén r: Sport.
- Saját megfogalmazás, elegendő ellenőrzött tény, elkülönített vélemény. Ne legyen kitalált adat, forrás vagy képcredit.
- Kép csak igazolt felhasználási joggal; egyébként üres img mező és a frontend saját illusztrációja.
- A régi azonosítókat és forráslinkeket meg kell őrizni. Publikálás előtt JSON, egyedi azonosítók és forrásduplikációk ellenőrzése.
- Közvetlen main commit friss blob SHA-val, utána visszaellenőrzés.

Kézi szerkesztés (pl. Claude-beszélgetésből) továbbra is lehetséges: a data/editorial.json items tömbjébe kerülnek az új elemek. A hirpult.com oldalát a workflow frissíti; a claude.ai-artifact már nem része az automatikus folyamatnak.
