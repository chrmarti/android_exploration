FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Set environment variables
ENV ANDROID_HOME=/opt/android-sdk
ENV GRADLE_HOME=/opt/gradle
ENV PATH=$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$ANDROID_HOME/tools/bin:$GRADLE_HOME/bin:$PATH
ENV DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    openjdk-17-jdk \
    wget \
    git \
    libgl1-mesa-dev \
    libxcursor1 \
    libnss3 \
    libxrandr2 \
    libxkbcommon0 \
    libxcomposite1 \
    libxi6 \
    libxtst6 \
    libpulse0 \
    && rm -rf /var/lib/apt/lists/*

# Create Android SDK directory
RUN mkdir -p $ANDROID_HOME/cmdline-tools

# Download and install the latest Android command-line tools
RUN wget -q https://dl.google.com/android/repository/commandlinetools-linux-10406996_latest.zip -O android-tools.zip && \
    unzip android-tools.zip -d $ANDROID_HOME/cmdline-tools && \
    mv $ANDROID_HOME/cmdline-tools/cmdline-tools $ANDROID_HOME/cmdline-tools/latest && \
    rm android-tools.zip

# Accept licenses and install SDK components
RUN yes | sdkmanager --licenses && \
    sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0" "system-images;android-34;google_apis;x86_64" "emulator"

# Install Gradle (latest version)
RUN wget -q https://services.gradle.org/distributions/gradle-8.6-bin.zip -O gradle.zip && \
    unzip gradle.zip -d /opt/ && \
    mv /opt/gradle-* $GRADLE_HOME && \
    rm gradle.zip

# Verify installations
RUN java -version && gradle --version && sdkmanager --version

# Create kvm group with GID 993
RUN groupadd -g 993 kvm
RUN usermod -aG kvm vscode

# Set up emulator device
USER vscode
RUN avdmanager create avd --name test_avd --package "system-images;android-34;google_apis;x86_64" --device "pixel_3a"
USER root

RUN chown -R vscode:vscode /opt
