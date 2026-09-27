# Havenly Help

A household-services booking experience.

## Frontend

The frontend is plain HTML, CSS, and JavaScript, packaged and served by Spring Boot. No Node.js installation or separate frontend server is needed.

The app includes a web app manifest, launcher icons, and a service worker that caches the app pages and static assets for offline opening. API requests are not cached. On supported browsers a conditional install button appears when installation is available; on iPhone/iPad, use the browser's Share menu and choose **Add to Home Screen**.

PWA install and service workers require a secure origin: `http://localhost:8080/` works on the host computer, and a deployed HTTPS domain works on other devices. The plain LAN URL (`http://192.168.x.x:8080/`) can display the website but browsers will not enable installation/offline service worker there; configure HTTPS for LAN use if you need PWA installation from another device.

Sign-in and account creation pages are available at `/login.html` and `/signup.html`. They are frontend prototypes only: there is no account/authentication API yet, and credentials are not sent or stored.

## Backend

The backend is a Spring Boot 3.4 API that requires Java 21 and Maven.

```powershell
cd backend
mvn spring-boot:run
```

Open the full application at `http://localhost:8080/`. The frontend and backend API share this port.

### Open it on another device on the same network

The computer running Spring Boot is the host. Keep it powered on and keep the backend running while using the app from another device.

Host requirements:

- Java 21 JDK
- Apache Maven 3.9 or newer
- Port 8080 available; allow inbound TCP port 8080 through the host firewall if Windows asks
- The host and client device connected to the same reachable Wi-Fi or local network

On the host computer, start the application:

```powershell
cd backend
mvn spring-boot:run
```

Find the host's Wi-Fi IPv4 address with `ipconfig` on Windows (look for `IPv4 Address` under the active Wi-Fi adapter). On each phone or computer on the same network, open `http://<host-ip>:8080/` in a browser. For example, this computer currently reports `http://192.168.1.2:8080/`; its address can change when the network reconnects.

If the page does not load, check that both devices are on the same non-guest network, Windows Firewall permits inbound TCP 8080, and the backend is still running. Corporate, guest, or client-isolated Wi-Fi can block device-to-device connections; use a private LAN or deploy to a reachable server instead. Do not expose this demo directly to the public internet.

Client devices need only a modern web browser; they do not need Java, Maven, or Node.js. For hosting on a different computer, install Java 21 and Maven there and start the backend on that computer, or build the Spring Boot JAR on a build machine and copy the JAR to the host. The frontend files are packaged inside the backend build.

The service catalog API runs at `http://localhost:8080/api/services`.

Additional endpoints:

- `GET /api/helpers` returns the available demo helpers.
- `GET /api/bookings` lists booking requests held in the current process.
- `POST /api/bookings` creates a booking request. The frontend submits here; the service/helper must exist in the catalog, the date must be today or later, and the time must match one of the available options.

Example request:

```json
{
	"serviceId": "deep-clean",
	"helperName": "Nia S.",
	"date": "2026-09-28",
	"time": "11:30 AM"
}
```

Bookings are stored in memory only and are cleared when the API restarts. Add a database before using this for real bookings or multi-user production use.
