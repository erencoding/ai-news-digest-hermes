# AI News Digest - 2026-05-05 Session Notes

## Session Date
2026-05-05

## Data Sources Status

| Source | Status | Method | Notes |
|--------|--------|--------|-------|
| The Decoder | ✅ Success | curl RSS + xml.etree | 8 items fetched successfully |
| Hacker News | ✅ Success | Firebase JSON API | 12 AI-related items filtered from top 80 |
| MarkTechPost | ❌ Failed | Python re (HTML parse) | Returned empty - confirmed Pitfall #11 |
| KDnuggets | ❌ Failed | Python re (HTML parse) | Returned empty - confirmed Pitfall #11 |
| Artificial Analysis | ✅ Success | Playwright | Used `document.body.innerText` extraction |

## Successful Patterns

### Playwright for Dynamic Content
```bash
NODE_PATH=/root/.hermes/node/lib/node_modules node -e "
const { chromium } = require('playwright');
(async () => {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();
  await page.goto('https://artificialanalysis.ai/', { 
    waitUntil: 'networkidle', 
    timeout: 60000 
  });
  await page.waitForTimeout(3000);
  const data = await page.evaluate(() => document.body.innerText);
  console.log(data);
  await browser.close();
})().catch(e => console.error(e.message));
"
```

**Key insight**: Extracting `document.body.innerText` is more reliable than parsing individual DOM elements for dynamically-loaded content.

### Hacker News Filtering
- Increased scan range from 50 to 80 IDs to ensure AI stories aren't missed
- Keywords list: `['ai','llm','gpt','claude','gemini','model','neural','openai','anthropic','deepmind','mistral','nvidia','gpu','machine learning','deep learning','transformer','agent']`
- Output limit: 15 items max

## Lessons Learned

1. **MarkTechPost/KDnuggets are unreliable** - Pitfall #11 confirmed again on 2026-05-05. These sources should be removed or replaced with RSS feeds.

2. **lark-cli auth check** - `HAS_LARK_USER=false` in this session. Skill requirement still enforced: return full content via message even when Feishu write fails.

3. **Model configuration** - Added `tencent/hy3-preview:free`, `anthropic/claude-opus-4.7`, `anthropic/claude-opus-4.6-fast` to Hermes config during this session (not part of digest skill, but notable context).

## Recommendation for Next Session

- **Remove MarkTechPost and KDnuggets** from data sources list
- **Add Hugging Face blog RSS**: `https://huggingface.co/blog/feed.xml`
- **Add MIT News AI RSS**: `https://news.mit.edu/topic/artificial-intelligence2-rss.xml`
- Keep The Decoder, Hacker News, and Artificial Analysis as primary sources
