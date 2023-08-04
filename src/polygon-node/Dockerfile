

FROM ubuntu:22.04




# Layer | System Dependencies
# =============================================================================

RUN set -eux; \
    # Disable the installation of optional dependencies for all invocations of apt-get
    echo "APT::Install-Suggests 'false';" >> /etc/apt/apt.conf.d/00-docker; \
    echo "APT::Install-Recommends 'false';" >> /etc/apt/apt.conf.d/00-docker; \
    echo "APT::Get::Assume-Yes 'true';" >> /etc/apt/apt.conf.d/00-docker; \
	echo "DPkg::Options '--force-confnew';" >> /etc/apt/apt.conf.d/00-docker; \
    # Install System Dependencies
    apt-get update --assume-yes --quiet && \
    apt-get upgrade --assume-yes --quiet && \
    apt-get install --assume-yes --quiet --no-install-recommends \
        build-essential \
        ca-certificates \
        wget \
		curl \
        # cmake \
		# locales \
		sudo \
    && \
    # Clean up
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -rf /tmp/* && \
    rm -rf /var/tmp/*



# Install Heimdall Binaries

RUN \
    curl -L https://raw.githubusercontent.com/maticnetwork/install/main/heimdall.sh | bash -s \
        -- <heimdall_version> <network_type> <node_type> && \
    heimdalld version --long && \
    # Configure heimdall seeds (Mainnet)
    # sed -i 's|^seeds =.*|seeds = "2a53a15ffc70ad41b6876ecbe05c50a66af01e20@3.211.248.31:26656,6f829065789e5b156cbbf076f9d133b4d7725847@3.212.183.151:26656,7285a532bad665f051c0aadc31054e2e61ca2b3d@3.93.224.197:26656,0b431127d21c8970f1c353ab212be4f1ba86c3bf@184.73.124.158:26656,f4f605d60b8ffaaf15240564e58a81103510631c@159.203.9.164:26656,31b79cf4a628a4619e8e9ae95b72e4354c5a5d90@44.232.55.71:26656,a385dd467d11c4cdb0be8b51d7bfb0990f49abc3@35.199.4.13:26656,daad548c0a163faae1d8d58425f97207acf923fd@35.230.116.151:26656,81c76e82fcc3dc9a0a1554a3edaa09a632795ea8@35.221.13.28:26656"|g' /var/lib/heimdall/config/config.toml && \
    # chown heimdall /var/lib/heimdall
    # Configure heimdall seeds (Mumbai)
    sed -i 's|^seeds =.*|seeds = "e72c0466a02ea43b2198bd3a9454b87a3ef0d77e@54.147.31.250:26656,b302d1ddb21102e794b524d05152a7834da05bd1@34.226.134.117:26656,9dfc12d9f39257cefc3d57a4d7302586e59a994e@18.213.200.99:26656"|g' /var/lib/heimdall/config/config.toml && \
    chown heimdall /var/lib/heimdall

# Install Bor Binaries

RUN \
    curl -L https://raw.githubusercontent.com/maticnetwork/install/main/bor.sh | bash -s -- <bor_version> <network_type> <node_type> && \
    bor version && \
    # Configure bor seeds (mainnet)
    sed -i 's|.*\[p2p.discovery\]|  \[p2p.discovery\] |g' /var/lib/bor/config.toml && \
    sed -i 's|.*bootnodes =.*|    bootnodes = ["enode://0cb82b395094ee4a2915e9714894627de9ed8498fb881cec6db7c65e8b9a5bd7f2f25cc84e71e89d0947e51c76e85d0847de848c7782b13c0255247a6758178c@44.232.55.71:30303","enode://88116f4295f5a31538ae409e4d44ad40d22e44ee9342869e7d68bdec55b0f83c1530355ce8b41fbec0928a7d75a5745d528450d30aec92066ab6ba1ee351d710@159.203.9.164:30303","enode://4be7248c3a12c5f95d4ef5fff37f7c44ad1072fdb59701b2e5987c5f3846ef448ce7eabc941c5575b13db0fb016552c1fa5cca0dda1a8008cf6d63874c0f3eb7@3.93.224.197:30303","enode://32dd20eaf75513cf84ffc9940972ab17a62e88ea753b0780ea5eca9f40f9254064dacb99508337043d944c2a41b561a17deaad45c53ea0be02663e55e6a302b2@3.212.183.151:30303"]|g' /var/lib/bor/config.toml && \
    chown bor /var/lib/bor && \
    # Configure bor seeds (mumbai)
    # sed -i 's|.*\[p2p.discovery\]|  \[p2p.discovery\] |g' /var/lib/bor/config.toml && \
    # sed -i 's|.*bootnodes =.*|    bootnodes = ["enode://320553cda00dfc003f499a3ce9598029f364fbb3ed1222fdc20a94d97dcc4d8ba0cd0bfa996579dcc6d17a534741fb0a5da303a90579431259150de66b597251@54.147.31.250:30303","enode://f0f48a8781629f95ff02606081e6e43e4aebd503f3d07fc931fad7dd5ca1ba52bd849a6f6c3be0e375cf13c9ae04d859c4a9ae3546dc8ed4f10aa5dbb47d4998@34.226.134.117:30303"]|g' /var/lib/bor/config.toml && \
    # chown bor /var/lib/bor && \
    # Update service config user permission
    sed -i 's/User=heimdall/User=root/g' /lib/systemd/system/heimdalld.service && \
    sed -i 's/User=bor/User=root/g' /lib/systemd/system/bor.service


# Start Services
RUN service heimdalld start
# Once Heimdall is synced, run the below command:
RUN service bor start


# Ports and Firewall Setup
# Open ports 22, 26656 and 30303 to world (0.0.0.0/0) on sentry node firewall.

# You can use VPN to restrict access for port 22 as per your requirement and security guidelines.
