<p align="center">
  <img src="./assets/next-action-pr-header.png" alt="Next Action PR by Gabo" width="830" />
</p>

<h1 align="center">Next Action PR</h1>

<p align="center">
  CLI tool to generate actionable Pull Request health reports for engineering teams.
</p>

<p align="center">
  <strong>Know who owns the next action before a Pull Request gets stuck.</strong>
</p>

---

## Overview

Next Action PR is a lightweight CLI tool that helps engineering teams generate clear, action-oriented Pull Request health reports.

Instead of creating another personal dashboard, this project focuses on producing a shared Markdown report that can be copied into tools like Confluence.

The report groups open Pull Requests by their next required action.

- Ready to merge
- Waiting for review
- Waiting for developer updates
- Old Pull Requests
- Unknown or unclear status

---

## Why This Exists

GitHub already shows Pull Requests, but teams often still need a simple shared report that answers one practical question:

> Who needs to do something next?

Next Action PR is designed to make that answer visible quickly.

---

## Documentation

- [01 - Project Plan](./docs/01-project-plan.md)

---

## Future Direction

The initial CLI is intended to be free and open source.

Future features may include:

- Direct Confluence publishing
- GitHub App integration
- Scheduled automation
- Reusable GitHub Action
- Advanced reporting workflows that run on the customer’s own infrastructure

---

## Credits

Header image based on a photo by <a href="https://unsplash.com/@praveentcom?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Praveen Thirumurugan</a> on <a href="https://unsplash.com/photos/a-book-and-a-small-figurine-on-a-desk-KPAQpJYzH0Y?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>.

Pull request icon from <a href="https://www.svgrepo.com/svg/363653/git-pull-request-duotone">SVG Repo</a>. Vectors and icons by <a href="https://github.com/phosphor-icons/phosphor-icons?ref=svgrepo.com">Phosphor</a> in MIT License via <a href="https://www.svgrepo.com/">SVG Repo</a>.

---

## License

This project is licensed under the MIT License.

The CLI is intended to remain free and open source. Future integrations, automation features, or advanced reporting workflows may be offered under separate commercial terms.
