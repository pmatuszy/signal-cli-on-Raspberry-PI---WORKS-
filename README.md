# signal-cli-on-Raspberry-PI THIS ONE WORKS

Instructions how to install signal-cli on Raspberry Pi - this one WORKS

After many attempts I finally wrote the procedure which you can just follow and get your signal-cli 

 newest version is here: https://github.com/AsamK/signal-cli/releases
```console
{
export VERSION=0.12.7
temp_catalog=/tmp
}
download the latest version of protoc from:

https://github.com/protocolbuffers/protobuf/releases
```

```console
{
echo ; echo "VERSION = $VERSION" 
echo "temp_catalog = $temp_catalog" ; echo 
}

```

```console
{
apt install -y curl zip protobuf-compiler clang libclang-dev cmake make
apt install -y openjdk-17-jdk
}

```

```console
{
cd /opt
rm -v signal-cli-"${VERSION}"-Linux-native.tar.gz* /opt/signal-cli 2>/dev/null
}

```

```console
{
wget https://github.com/AsamK/signal-cli/releases/download/v"${VERSION}"/signal-cli-"${VERSION}".tar.gz
tar xf signal-cli-"${VERSION}".tar.gz -C /opt
rm -v signal-cli-"${VERSION}".tar.gz
}
#
```
```console
(
ln -sf /opt/signal-cli-"${VERSION}"/bin/signal-cli /usr/local/bin/
ln -s /opt/signal-cli-"${VERSION}" /opt/signal-cli
ls -l /usr/local/bin/signal-cli /opt/signal-cli
)
```
```console
curl https://sh.rustup.rs -sSf | sudo sh -s -- --default-toolchain nightly-aarch64-unknown-linux-gnu -y
```

```console
export PATH=$PATH:$HOME/.cargo/bin
```
```console
{
mkdir -p ${temp_catalog}/signal-cli-install && cd ${temp_catalog}/signal-cli-install
export LIBVERSION=$(find /opt/signal-cli-"${VERSION}"/lib/ -maxdepth 1 -mindepth 1 -name 'libsignal-client-*' | sed -E 's/\/opt\/signal-.*libsignal-client-//g' | sed -E 's/.jar//g')
echo ; echo "LIBVERSION = $LIBVERSION" ; echo
}
```
```console
{
wget https://github.com/signalapp/libsignal/archive/refs/tags/v"${LIBVERSION}".tar.gz ; echo $?
mv -v v"${LIBVERSION}".tar.gz ${temp_catalog}/signal-cli-install
tar xzf ${temp_catalog}/signal-cli-install/v"${LIBVERSION}".tar.gz -C ${temp_catalog}/signal-cli-install/ && mv ${temp_catalog}/signal-cli-install/libsignal-"${LIBVERSION}" libsignal
rm -v ${temp_catalog}/signal-cli-install/v"${LIBVERSION}".tar*
cd libsignal/java
sed -i "s/include ':android'//" ${temp_catalog}/signal-cli-install/libsignal/java/settings.gradle
${temp_catalog}/signal-cli-install/libsignal/java/build_jni.sh desktop
zip -d /opt/signal-cli-${VERSION}/lib/libsignal-client-*.jar libsignal_jni.so
zip /opt/signal-cli-${VERSION}/lib/libsignal-client-*.jar ${temp_catalog}/signal-cli-install/libsignal/target/release/libsignal_jni.so
}
```

```console
mkdir -p /usr/java/packages/lib
```
```console
cp -v ${temp_catalog}/signal-cli-install/libsignal/target/release/libsignal_jni.so /usr/java/packages/lib ; echo $?
```
```console
rm -rv ${temp_catalog}/signal-cli-install
```
```console
{
chown root:root /usr/java/packages/lib/libsignal_jni.so ; echo $?
chmod 755 /usr/java/packages/lib/libsignal_jni.so ; echo $?
chmod 755 -R /opt/signal-cli-${VERSION} ; echo $?
chown root:root -R /opt/signal-cli-${VERSION} ; echo $?
}
```
