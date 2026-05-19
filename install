#!/bin/sh
# CodeStrain installer. Source: https://github.com/codestrain/codestrain-cli/blob/main/install.sh
# Usage: curl -fsSL codestrain.dev/install | sh
# Env:   CODESTRAIN_VERSION=0.1.0  (default: latest)

set -eu

PKG="codestrain"
VERSION="${CODESTRAIN_VERSION:-}"

say()  { printf '%s\n' "codestrain: $*" >&2; }
fail() { say "error: $*"; exit 1; }
have() { command -v "$1" >/dev/null 2>&1; }

check_python() {
    have python3 || fail "python3 not found. Install Python 3.9+ first."
    python3 - <<'PY' || fail "Python 3.9+ required (you have $(python3 --version 2>&1))."
import sys
sys.exit(0 if sys.version_info >= (3, 9) else 1)
PY
}

spec() {
    if [ -n "$VERSION" ]; then
        printf '%s==%s' "$PKG" "$VERSION"
    else
        printf '%s' "$PKG"
    fi
}

path_hint() {
    case ":${PATH:-}:" in
        *":$HOME/.local/bin:"*) : ;;
        *) say "note: add \$HOME/.local/bin to PATH so the 'codestrain' command is found." ;;
    esac
}

main() {
    pkg="$(spec)"

    # Preference order:
    #   macOS with brew → brew tap (clean PATH, native macOS conventions)
    #   pipx           → isolated venv + ~/.local/bin symlink
    #   uv tool        → fastest, same outcome as pipx
    #   pip --user     → fallback, requires manual PATH hint
    if [ "$(uname -s)" = "Darwin" ] && have brew; then
        say "installing $pkg via Homebrew"
        if ! brew tap | grep -q '^codestrain/tap$'; then
            brew tap codestrain/tap
        fi
        brew install --quiet codestrain/tap/codestrain
        say "done. Run: codestrain --help"
        return
    fi

    check_python

    if have pipx; then
        say "installing $pkg via pipx"
        pipx install --force "$pkg"
    elif have uv; then
        say "installing $pkg via uv tool"
        uv tool install --force "$pkg"
    else
        say "installing $pkg via pip --user (pipx/uv not found)"
        python3 -m pip install --user --upgrade "$pkg"
        path_hint
    fi

    say "done. Run: codestrain --help"
}

main
