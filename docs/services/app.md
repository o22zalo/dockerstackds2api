# App service (`compose.apps.yml`)

## Vai trò
- Service ứng dụng chính, chạy DS2API bằng image `ghcr.io/cjackhwang/ds2api:latest`.
- Compose service vẫn tên `app` để giữ nguyên Core/Ops/Access của stack.

## Cấu hình chính
- Port expose localhost: `127.0.0.1:${APP_HOST_PORT}:${APP_PORT}`.
- Data volume: `${DOCKER_VOLUMES_ROOT:-./.docker-volumes}/app/data:/data`.
- Logs volume: `${DOCKER_VOLUMES_ROOT:-./.docker-volumes}/app/logs:/app/logs`.
- Healthcheck: `wget http://localhost:${APP_PORT}${HEALTH_PATH}`.

## ENV bắt buộc
- `APP_PORT`: port app lắng nghe trong container, mặc định `5001`.
- `PROJECT_NAME`, `DOMAIN`: tạo hostname public.
- `CADDY_AUTH_USER`, `CADDY_AUTH_HASH`: basic auth.
- `DS2API_ADMIN_KEY`: admin key của DS2API.
- `DS2API_CONFIG_JSON`: nội dung `config.json` đã encode base64.

## ENV optional
- `APP_HOST_PORT` (default `6011`): chỉ truy cập localhost host machine.
- `HEALTH_PATH` (default `/healthz`).
- `DS2API_TZ` (default `Asia/Ho_Chi_Minh`).
- `DS2API_LOG_LEVEL` (default `INFO`).
- `DS2API_CONFIG_PATH` (default `/data/config.json`).
- `DS2API_API_KEY`, `DS2API_MODEL`: giá trị tiện dụng cho client/test.
- `DOCKER_VOLUMES_ROOT` (default `./.docker-volumes`).
- `TAILSCALE_TAILNET_DOMAIN`: dùng cho route HTTPS nội bộ qua caddy_1.

## Routing
- Public host: `${PROJECT_NAME}.${DOMAIN}` (+ alias).
- Internal HTTPS host: `${PROJECT_NAME}.${TAILSCALE_TAILNET_DOMAIN}` với `tls internal`.
