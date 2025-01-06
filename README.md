# Github Action for pushing a Narrat game to itch.io

This repository serves as an example of how to build a CD pipeline to upload build of your narrat game to Itch.io.

## Example usage

The following is an example to ship your game when a new release tag is created:

```yaml
name: Deploy

on:
  push:
    tags:
      - "release-*"
env:
  ITCH_USERNAME: your-username
  ITCH_GAME_ID: game-name
jobs:
  deploy:
    runs-on: ubuntu-latest
    name: Deploy to Itch.io
    strategy:
      fail-fast: true
      matrix:
        channel:
          - html
    steps:
      - uses: actions/checkout@v3
      - name: Install node
        uses: actions/setup-node@v4
        with:
          node-version: 23
      - name: Install dependencies
        run: npm install
      - name: Build
        run: npm run build
      - uses: KikimoraGames/itch-publish@v0.0.3
        with:
          butlerApiKey: ${{ secrets.BUTLER_API_KEY }}
          gameData: ./dist
          itchUsername: ${{ env.ITCH_USERNAME }}
          itchGameId: ${{ env.ITCH_GAME_ID }}
          buildChannel: ${{ matrix.channel }}
          buildNumber: ${{ github.ref_name }}
```

### Required variables

| Name               | Description                                                                                                                                                             |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ITCH_USERNAME**  | Found in your game url (lore-ipsum.itch.io/your-game-id)                                                                                                                |
| **ITCH_GAME_ID**   | Found in your itch.io url (your-username.itch.io)                                                                                                                       |
| **BUTLER_API_KEY** | Itch.io API key found [here](https://itch.io/docs/butler/login.html#running-butler-from-ci-builds-travis-ci-gitlab-ci-etc). Must be setup in your repository variables. |

## Credits

This action has been loosely based on the work done by [KikimoraGames](https://github.com/marketplace/actions/itch-io-publish).
