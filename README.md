# OpenVINO Toolkit SDK for Workshop

This SDK provides the Intel OpenVINO™ Runtime, Python bindings, and Intel GPU driver support for
AI inference workloads in Workshop. OpenVINO runs on CPU by default and is fully functional
without dedicated hardware; the `gpu` plug enables hardware-accelerated inference on compatible
Intel GPUs. arm64 is listed as a build target but setup scripts target amd64; arm64 support is experimental and untested.

---

## Reference workshop

A minimal workshop:

```yaml
# workshop.yaml
name: openvino-app
base: ubuntu@24.04
sdks:
  - name: openvino
    channel: 2025/stable

actions:
  verify: |
    python3 -c "from openvino import Core; print(Core().available_devices)"
```

This demonstrates a minimal OpenVINO environment that verifies the runtime is available.

---

## Using the SDK

### Prerequisites, project layout

1. No prerequisite SDKs are required.
2. No specific project layout is needed; OpenVINO can be used with any Python or C++ project.
3. On launch, the SDK installs Intel GPU compute drivers from the
   `ppa:kobuk-team/intel-graphics` PPA, configures `ldconfig` for OpenVINO shared libraries,
   and registers the Python bindings via a `.pth` file. No user action is needed.

### Verify the runtime

Once the workshop is ready:

```bash
workshop shell
python3 -c "from openvino import Core; print(Core().available_devices)"
```

Expected output: `['CPU']` without the `gpu` plug, or `['CPU', 'GPU']` with it enabled.

To run a self-contained inference example, download a small ONNX model and compile it:

```bash
workshop shell
# Download a small test model
wget -q https://github.com/onnx/models/raw/main/validated/vision/classification/squeezenet/model/squeezenet1.0-3.onnx -O model.onnx
python3 -c "
from openvino import Core
core = Core()
model = core.read_model('model.onnx')
compiled = core.compile_model(model, 'CPU')
print('Model compiled successfully on', 'CPU')
"
```

### Check device availability

From within the workshop shell:

```bash
workshop shell
python3 -c "
from openvino import Core
core = Core()
print('CPU available:', 'CPU' in core.available_devices)
print('GPU available:', 'GPU' in core.available_devices)
"
```

---

## Plugs (resources this SDK consumes)

### `gpu`

- Interface: `gpu`
- Purpose: Grants access to Intel GPU hardware on the host for hardware-accelerated inference.

OpenVINO runs on CPU by default and is fully functional without this plug.
Adding the `gpu` plug enables the `GPU` device for inference. To verify GPU availability:

```bash
python3 -c "from openvino import Core; print(Core().available_devices)"
```

If GPU is detected, `GPU` appears in the output list alongside `CPU`.

## Slots (resources this SDK provides)

### `openvino-libs`

- Interface: `mount`
- Workshop source: `$SDK`
- Purpose: Exposes the entire SDK install tree (OpenVINO libraries, Python bindings, pkgconfig
  files) to consumer SDKs.

No SDK in the current store consumes this slot. A consumer SDK would declare a matching plug to
mount the OpenVINO libraries into its own environment:

```yaml
# workshop.yaml (illustrative -- no consumer SDK exists today)
name: openvino-consumer
base: ubuntu@24.04
sdks:
  - name: openvino
    channel: 2025/stable
  - name: my-inference-app
    channel: all/edge
```

---

## Documentation and guidance

- [OpenVINO official documentation](https://docs.openvino.ai)
- [Workshop documentation](https://ubuntu.com/workshop/docs/)

---

## Community and support

- OpenVINO community: [OpenVINO GitHub](https://github.com/openvinotoolkit/openvino)
- Intel developer forums:
  [Intel Developer Community](https://community.intel.com/t5/Intel-Distribution-of-OpenVINO/bd-p/distribution-openvino-toolkit)
- Workshop forum:
  [Discourse](https://discourse.ubuntu.com/)
- Please review our
  [Code of Conduct](https://ubuntu.com/community/ethos/code-of-conduct) before participating.

---

## Contributions

All contributions, including code, documentation updates, and issue reports, are welcome!

- See `CONTRIBUTING.md` for guidelines.
- Open issues or pull requests on the official repository.

---

## License and copyright

Copyright 2025 Canonical Ltd.

The OpenVINO Toolkit is licensed under the
[Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0).

Intel, the Intel logo, Intel Atom, Intel Core, Intel Xeon Phi, Iris, OpenVINO, the OpenVINO logo, Pentium, VTune, and Xeon are trademarks of Intel Corporation or its subsidiaries.
