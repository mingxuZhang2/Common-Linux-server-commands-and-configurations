## Usage

When we are not in campus, we need to use ivanti VPN to connect to our network. But the ivanti VPN is not stable and will occupy the port so we are not able to use the Clash X or other VPN tools to use the ChatGPT or other services. 

Thanks to [yeahjack](https://github.com/yeahjack/hkust-gz-vpn-docker), we are able to use the docker to connect the HKUST-GZ VPN and use Clash at the same time.

## Tutorials

### Step 1: Install Docker

Firstly, we need to install Docker on our local machine. You can download the Docker from the [official website](https://www.docker.com/products/docker-desktop). And you can choose your own operating system to download the Docker.

### Step 2: Clone the Repository

After we finish the installation of Docker, we need to clone the docker file from the repository.

```bash
git clone https://github.com/yeahjack/hkust-gz-vpn-docker
```

### Step 3: Setup your own configuration

We need to modify the `config.env` file to set up our own configuration. You can use the following command to open the file, or just use some text editor to open the file.

```bash
move config.env.example config.env
vim config.env
```

We need to fill our password and username in the `config.env` file. And we can also modify the `authgroup` to choose the role. The default role is `Student`, if you are a staff, you can change it to `Staff`.

### Step 4: Build the Docker Image

After we finish the configuration, we can build the docker image by using the following command.

```bash
docker build -t hkust-gz-vpn .
```

### Step 5: Run the Docker Container

After we finish the build, we can run the docker container by using the following command.

```bash
docker run -d --name hkust-gz-vpn --env-file=config.env --privileged -p 11080:1080 hkust-gz-vpn
```

The command will build your own container. If there are something wrong, you can use the following command use the existing container.

```bash
# For Windows Users
docker run -d --name hkust-gz-vpn --env-file=config.env --privileged -p 11080:1080 yeahjack/hkust-gz-vpn:amd64
# For Mac Users
docker run -d --name hkust-gz-vpn --env-file=config.env --privileged -p 11080:1080 yeahjack/hkust-gz-vpn:m1_arm64
```

And once you finish the command, the container will successfully store in your local machine, next time you can use the following command to start the container.

```bash
docker start hkust-gz-vpn
```

Or just open the Docker Desktop and start the container. Like the image below.

![docker](fig/image2.png)

### Step 6: Check the running status

We can see the running logs by using the following command.

```bash
docker logs hkust-gz-vpn
```

Or just open the log by clicking the container in the Docker Desktop. 

### Step 7: Use the VPN

We usually use the VPN to connect the server. We need to set up the SSH port forwarding to use the VPN. We need to open the VScode SSH config and change some configurations.
    
```json
Host hkust-gz
    HostName YOUR_SERVER_IP
    User YOUR_USERNAME
    Port 11080
    ProxyCommand nc -x localhost:1080 %h %p
```


---

## 🛠 Step-by-Step Tutorial: Using ClashX with ZeroOmega Without System Proxy (macOS + Ivanti VPN)

There is also another solution for this scenario:  
**If we use Ivanti, we cannot access ClashX, especially on macOS.**  
Previously, we usually relied on **ClashX as the system proxy**, but this often causes conflicts with Ivanti VPN.  
This guide shows how to use **ZeroOmega** (an enhanced version of SwitchyOmega) to route **only browser traffic** through ClashX, while keeping the system proxy disabled.

---

### 🔧 Step 1: Install ZeroOmega in Chrome

1. Open Chrome.
2. Visit the **ZeroOmega GitHub Releases Page** (search “ZeroOmega GitHub”).
3. Download the `.zip` or `.crx` file for the latest version.
4. Go to `chrome://extensions/`.
5. Enable **Developer Mode** (top right).
6. Click **“Load unpacked”** and select the unzipped folder  
   _or_ drag the `.crx` file into Chrome to install.
7. The ZeroOmega icon should now appear in your Chrome toolbar.

---

### ⚙️ Step 2: Configure ClashX (Enable Necessary Ports)

1. Launch **ClashX** on macOS.
2. Click the menu bar icon → **Preferences** → **General**.
3. Ensure the following settings:
   - ✅ **Allow connections from LAN**
   - ✅ **Enable HTTP Proxy** (default port: `7890`)
   - Optional: Enable **SOCKS5 Proxy** (port `7891`) if you prefer SOCKS5
4. ❌ **Do not enable “Set as System Proxy”**  
   This avoids conflict with Ivanti VPN.

---

### 🧩 Step 3: Set Up ZeroOmega Proxy Profile

**Option A: Use HTTP Proxy (Recommended)**

1. Click the ZeroOmega extension icon.
2. Go to **Options** → Add Profile → **Fixed Profile**.
3. Name the profile: `proxy`.
4. Under “Proxy Rules”, configure:
   - **Protocol:** `HTTP`
   - **Server:** `127.0.0.1`
   - **Port:** `7890`
5. Click **Save**.

**Option B: Use SOCKS5 Proxy**

> _Only use this if SOCKS5 is enabled in ClashX._

- **Protocol:** `SOCKS5`
- **Port:** `7891`

---

### 🧪 Step 4: Test Your Setup

1. Click the ZeroOmega icon.
2. Switch to your `proxy` profile.
3. Visit websites like:
   - https://www.google.com
   - https://www.youtube.com
4. If they load correctly, your browser is routing traffic through ClashX successfully.

---

### 🚫 Step 5: Keep System Proxy Disabled

- Do **not** enable system proxy in ClashX.
- Ivanti VPN will continue handling internal/intranet access.
- Only **browser traffic** (via ZeroOmega) uses ClashX for external sites.

---

## ✅ Result

You have successfully:

- ✅ Avoided system-level proxy conflicts with Ivanti
- ✅ Routed browser traffic (e.g. Chrome) through ClashX
- ✅ Maintained internal resource access via Ivanti VPN
- ✅ Accessed external sites (Google, YouTube) freely

---

## 💡 Bonus: ZeroOmega Profile JSON (Optional)

If you'd like to import a proxy profile via JSON:

```json
{
  "+proxy": {
    "bypassList": [
      { "conditionType": "BypassCondition", "pattern": "127.0.0.1" },
      { "conditionType": "BypassCondition", "pattern": "::1" },
      { "conditionType": "BypassCondition", "pattern": "localhost" }
    ],
    "color": "#99ccee",
    "fallbackProxy": {
      "host": "127.0.0.1",
      "port": 7890,
      "scheme": "http"
    },
    "name": "proxy",
    "profileType": "FixedProfile"
  },
  "-startupProfileName": "proxy",
  "schemaVersion": 2
}


