Create a folder
$ mkdir actions-runner && cd actions-runner
# Download the latest runner package
$ curl -o actions-runner-linux-arm64-2.314.1.tar.gz -L https://github.com/actions/runner/releases/download/v2.314.1/actions-runner-linux-arm64-2.314.1.tar.gz
# Optional: Validate the hash
$ echo "3d27b1340086115a118e28628a11ae727ecc6b857430c4b1b6cbe64f1f3b6789  actions-runner-linux-arm64-2.314.1.tar.gz" | shasum -a 256 -c
# Extract the installer
$ tar xzf ./actions-runner-linux-arm64-2.314.1.tar.gz
Configure
# Create the runner and start the configuration experience
$ ./config.sh --url https://github.com/grateful345/Wiz-Go-call-sign --token BHAHZGAGUMQWPRRZLTXXCZLF6URSO
# Last step, run it!
$ ./run.sh
Using your self-hosted runner
# Use this YAML in your workflow file for each job
runs-on: self-hosted

Download
# Create a folder
$ mkdir actions-runner && cd actions-runner
# Download the latest runner package
$ curl -o actions-runner-osx-x64-2.314.1.tar.gz -L https://github.com/actions/runner/releases/download/v2.314.1/actions-runner-osx-x64-2.314.1.tar.gz
# Optional: Validate the hash
$ echo "3faff4667d6d12c41da962580168415d628e3ffba9924b9ac995752087efc921  actions-runner-osx-x64-2.314.1.tar.gz" | shasum -a 256 -c
# Extract the installer
$ tar xzf ./actions-runner-osx-x64-2.314.1.tar.gz
Configure
# Create the runner and start the configuration experience
$ ./config.sh --url https://github.com/grateful345/Wiz-Go-call-sign --token BHAHZGDHHICG3LFF53OICRLF6UR24
# Last step, run it!
$ ./run.sh
Using your self-hosted runner
# Use this YAML in your workflow file for each job
runs-on: self-hosted
