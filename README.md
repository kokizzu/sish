# sish

Open source SSH tunneling for HTTP(S), WS(S), TCP, aliases, and SNI.

If you like the simplicity of serveo/ngrok-style sharing but want to use plain
SSH and run your own infrastructure, `sish` is built for that.

- No custom client required for end users
- Public and private tunnel workflows
- Docker and binary releases
- Designed for production-grade self-hosting

[Docs](https://docs.ssi.sh) | [Managed Service](https://tuns.sh) | [Releases](https://github.com/antoniomika/sish/releases) | [Docker Images](https://hub.docker.com/r/antoniomika/sish/tags)

## Why sish

`sish` runs an SSH server focused on forwarding and multiplexing. Users connect
with commands they already know, and `sish` handles the routing.

### Typical use cases

- Share a local web app instantly over HTTPS
- Expose a TCP service to a fixed or random external port
- Create private TCP aliases that are only reachable through authenticated SSH
- Route TLS traffic by SNI to multiple backends without terminating TLS

## Quick Start

### 1) Try the managed service

The fastest way to validate your workflow:

```bash
ssh -R 80:localhost:8080 tuns.sh
```

This creates a public URL for your local app running on port `8080`.

### 2) Self-host with Docker

Pull image:

```bash
docker pull antoniomika/sish:latest
```

Prepare directories:

```bash
mkdir -p ~/sish/ssl ~/sish/keys ~/sish/pubkeys
cp ~/.ssh/id_ed25519.pub ~/sish/pubkeys
```

Run:

```bash
docker run -itd --name sish \
	-v ~/sish/ssl:/ssl \
	-v ~/sish/keys:/keys \
	-v ~/sish/pubkeys:/pubkeys \
	--net=host antoniomika/sish:latest \
	--ssh-address=:2222 \
	--http-address=:80 \
	--https-address=:443 \
	--https=true \
	--https-certificate-directory=/ssl \
	--authentication-keys-directory=/pubkeys \
	--private-keys-directory=/keys \
	--bind-random-ports=false \
	--domain=example.com
```

Then connect:

```bash
ssh -p 2222 -R 80:localhost:8080 example.com
```

## Forwarding Examples

### HTTP tunnel

```bash
ssh -R myapp:80:localhost:8080 tuns.sh
```

Your app at `localhost:8080` becomes available at `https://myapp.tuns.sh`.

### TCP tunnel

```bash
ssh -R 2222:localhost:22 tuns.sh
```

`localhost:22` is available at `tuns.sh:2222`

### Private TCP alias

```bash
ssh -R mylaptop:22:localhost:22 tuns.sh
```

Access from another client:

```bash
ssh -J tuns.sh mylaptop
```

## Feature Highlights

- HTTP(S), WS(S), TCP forwarding, and multiplexing
- TCP aliases for private internal-only access patterns
- SNI proxy support for TLS-based backend routing
- Optional load balancing modes for HTTP/TCP/SNI aliases
- Service console support for inspecting forwarded requests
- Key and password authentication with dynamic key reloading
- Restrictive binding policies for safer multi-tenant setups

## Local Development

Clone:

```bash
git clone git@github.com:antoniomika/sish.git
cd sish
```

Start locally:

```bash
go run main.go --http-address localhost:3000 --domain testing.ssi.sh
```

Or use:

```bash
make dev
```

Test a tunnel:

```bash
ssh -p 2222 -R 80:localhost:8080 testing.ssi.sh
```

`testing.ssi.sh` is configured to point to localhost for development.

## Learn More

- [Getting Started](https://docs.ssi.sh/getting-started)
- [Forwarding Types](https://docs.ssi.sh/forwarding-types)
- [CLI Reference](https://docs.ssi.sh/cli)
- [FAQ](https://docs.ssi.sh/faq)

## Community

- IRC: [#sish on libera](https://web.libera.chat/#sish) / [#pico.sh on libera](https://web.libera.chat/#pico.sh)
- Email: [me@antoniomika.me](mailto:me@antoniomika.me)
