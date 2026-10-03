# Platform Testing

OS-specific behavior must be verified on the OS that executes it. A Linux
container is not Windows verification.

## Host decision

| Host | Windows-specific test environment | Rule |
| --- | --- | --- |
| Windows | The actual Windows host | Run PowerShell, Windows Node, installer, and CLI checks natively. Do not use WinBoat or Docker as a substitute. |
| Linux with KVM and WinBoat prerequisites | WinBoat's supported Docker Compose backend | Start the real Windows guest, run the project command inside it, and collect guest evidence. |
| Linux without KVM, macOS, or another unsupported host | A real Windows VM or Windows CI runner | If none is available, report Windows verification as `NOT RUN`; do not call a Linux container Windows. |

Detect the host from the runtime that launches the check, not from the target
being tested. Record both host and guest identity in the result.

## WinBoat preflight

On a non-Windows host, use WinBoat only when all applicable prerequisites pass:

- Docker Engine and Docker Compose v2 are available and the daemon is running;
  Docker Desktop is not a substitute for the supported Linux setup.
- On Linux, hardware virtualization is enabled and `/dev/kvm` is accessible.
- As a baseline, WinBoat currently documents at least 4 GB RAM, 2 CPU threads,
  and 32 GB free storage. Leave additional headroom for the project test.
- Network access, permissions, and the project test inputs are available.
- The WinBoat installation and Windows image/license setup are already
  authorized. Never hide credentials in Compose files or logs.

Use WinBoat's supported setup and its generated Compose configuration. Start
that project with `docker compose -f <generated-compose-file> up -d`, wait for
Windows readiness, and run the project command inside the guest. Do not invent
a second Windows Compose stack that can drift from WinBoat. Stop it with the
same Compose project (`docker compose -f <generated-compose-file> down`) after
capturing evidence; preserve user-owned WinBoat state. If the preflight fails,
stop before provisioning and use a real Windows runner or report `NOT RUN`.

## Required execution evidence

A passing Windows check must include:

1. The host OS and architecture.
2. The WinBoat/guest Windows version and architecture, when WinBoat is used.
3. The exact command executed inside the Windows guest.
4. The exit code and relevant proving output.
5. The project behavior under test, not only a boot or connectivity check.
6. Cleanup of temporary Compose resources and test data, without deleting
   user-owned WinBoat state.

Use `PASS`, `FAIL`, or `NOT RUN` honestly. A guest that boots but never runs
the project command is not a completed Windows verification.

## Reporting template

```text
Host: <OS, version, architecture>
Windows environment: <native host | WinBoat Docker Compose | Windows CI/VM>
Guest: <Windows version, architecture, or N/A>
Command: <exact command>
Exit code: <number or NOT RUN>
Evidence: <short proving output>
Result: <PASS | FAIL | NOT RUN>
Cleanup: <what was removed or preserved>
```

For release or delivery decisions, a missing Windows row remains an explicit
verification gap even when Linux and macOS checks pass.
