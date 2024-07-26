# internet_speed_tester_with_python
Check the internet speed through python code.
here is the python code.
#!/usr/bin/env python3
import speedtest
import subprocess
import time
import sys

def get_network_info():
    try:
        # Get IP address and network interface info
        ip_info = subprocess.check_output(['hostname', '-I']).decode().strip()
        iface_info = subprocess.check_output(['ip', 'route', 'get', '1']).decode().split()
        iface_name = iface_info[4]
        if iface_name.startswith('eth') or iface_name.startswith('enp'):
            return f"LAN ({iface_name})"
        elif iface_name.startswith('wlan') or iface_name.startswith('wlp'):
            return f"Wi-Fi ({iface_name})"
        else:
            return f"Unknown Interface ({iface_name})"
    except Exception as e:
        print(f"Error retrieving network information: {e}")
        return "Unknown"

def test_internet_speed():
    try:
        # Perform speed test using speedtest-cli
        st = speedtest.Speedtest()
        st.get_best_server()
        download_speed = st.download() / 1_000_000  # Convert from bits/s to Mbps
        upload_speed = st.upload() / 1_000_000     # Convert from bits/s to Mbps
        return download_speed, upload_speed
    except Exception as e:
        print(f"Speed test failed: {e}")
        return None, None

def main():
    try:
        print("Press Ctrl+C to stop.")
        while True:
            network_info = get_network_info()
            download_speed, upload_speed = test_internet_speed()

            if download_speed is not None and upload_speed is not None:
                print(f"Network: {network_info}")
                print(f"Download Speed: {download_speed:.2f} Mbps")
                print(f"Upload Speed: {upload_speed:.2f} Mbps")
            else:
print("Failed to retrieve speed test results.")

            time.sleep(1)  # Wait for 1 second before fetching speed again

    except KeyboardInterrupt:
        print("\nSpeed test stopped by user.")

if __name__ == "__main__":
    main()
