# PRFAQ.MD - Press Release FAQ
# ComposerAtlas

**Version:** 1.1
**Last Updated:** 2026-06-08

---

## Press Release

**FOR IMMEDIATE RELEASE**

### ComposerAtlas Launches as the First Dedicated Strategy Library and Education Hub for Composer.trade Investors

*A free, browser-based reference site breaks down systematic investing strategies in plain language, helping retail investors understand the logic behind rules-based symphonies.*

**Las Vegas, NV - June 8, 2026**

ComposerAtlas today launches as a public, open-access website that curates and explains Composer.trade investing strategies for self-directed retail investors. Each strategy featured on the site receives a dedicated page with a full logic breakdown, performance metrics, and plain-English explanations of the signals and concepts that drive it.

Unlike raw backtest tools or academic papers, ComposerAtlas bridges the gap between knowing a strategy exists and understanding how and why it works. Visitors can explore concepts like RSI, 200-day moving averages, VIX tier rotation, momentum, and leveraged ETF mechanics through an integrated glossary that cross-links directly to real strategies using those concepts.

The site runs entirely in the browser with no server infrastructure, hosted on GitHub Pages at zero cost. Strategy data is maintained via a static JSON database updated by the curator. Visitors can clone any featured strategy directly on Composer.trade with a single click.

ComposerAtlas is free to use and supported by user donations and Google AdSense.

---

## Frequently Asked Questions

### Customer / User FAQ

**Q: What is ComposerAtlas?**
A: ComposerAtlas is a free reference website that showcases 12 curated Composer.trade strategies, explains how they work in plain language, and educates visitors on the investing concepts behind them.

**Q: Who is this for?**
A: Self-directed retail investors who use Composer.trade, are curious about systematic investing, or want to learn about concepts like RSI, VIX strategies, momentum, or leveraged ETFs.

**Q: Are these strategies financial advice?**
A: No. ComposerAtlas is an educational resource. All strategies are presented for informational purposes only. Past performance does not guarantee future results. Always do your own research before investing.

**Q: Can I clone these strategies on Composer.trade?**
A: Yes. Each strategy page includes a direct link to clone the symphony on Composer.trade.

**Q: Is ComposerAtlas free?**
A: Yes. The site is free to access. If you find it valuable, you can support development via a donation at https://azqato.github.io/support.html.

**Q: Who curates the strategies?**
A: Strategies are selected and maintained by the site owner. All featured strategies are provided with full transparency on their logic and metrics.

**Q: How often are metrics updated?**
A: Metrics are updated manually via GitHub commits. Each strategy page displays a last updated date.

**Q: Is ComposerAtlas affiliated with Composer.trade?**
A: No. ComposerAtlas is an independent, community-built resource. Composer.trade is a separate company and platform.

---

### Internal / Operational FAQ

**Q: Why build this as a static site?**
A: Zero server cost, zero maintenance overhead, maximum reliability. GitHub Pages is free, fast, and requires no infrastructure management.

**Q: Why JSON instead of a real database?**
A: At MVP scale (10-100 strategies), a flat JSON file is sufficient, fast, and requires no backend. The schema is designed to migrate easily to a real database if scale demands it.

**Q: How does the site make money?**
A: Google AdSense (post-MVP) and direct user donations via https://azqato.github.io/support.html.

**Q: What happens if Composer.trade changes their URLs?**
A: The `symphony_url` field in the JSON can be updated per strategy via a commit. A future script will automate this check.

**Q: How do I add a new strategy?**
A: See RUNBOOK.MD for the full process.

**Q: What is the long-term vision?**
A: Become the canonical public reference for Composer.trade strategy discovery and education, eventually hosting thousands of strategies with search, filtering, and comparison tools.
