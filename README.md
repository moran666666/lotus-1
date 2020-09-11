# How to use it

Command 'verify' manually trigger WinPoSt, with additional log in rust code you can find out which sectors fail in sanity check.

You don't need to stop runnig lotus-miner if you copy fsrepo to new path

> CAUTION: please backup important data before trying this command, and make sure your miner is not doing PoSt now(you can check that by `lotus-miner proving deadlines`)

### Copy fsrepo

lotus-miner will add a file lock on fsrepo when running, so we need to use a independent fsrepo, copy files/directories declare in 'node/repo/fsrepo.go' to new path

```golang
const(
	fsAPI           = "verify/api"
	fsAPIToken      = "verify/token"
	fsConfig        = "verify/config.toml"
	fsStorageConfig = "verify/storage.json"
	fsDatastore     = "verify/datastore"
	fsLock          = "verify/repo.lock"
  fsKeystore      = "verify/keystore
)
```

### Build rust deps

#### Modify code in local cargo registry

- local rust code locates at path like

  `~/.cargo/registry/src/mirrors.ustc.edu.cn-12df342d903acd47/storage-proofs-post-5.1.1/src/fallback/vanilla.rs`

- modify code follow this patch
  https://github.com/shaodan/rust-fil-proofs/commit/47360bf02090117359c9ee29d0284ee15f847c60

#### Compile deps

`FFI_BUILD_FROM_SOURCE=1 RUSTFLAGS='-C target-cpu=native -g' make clean deps`

### Build lotus-miner

`make lotus-miner`

### Run the command

export same ENVs your lotus-miner run with, i.e.

FULLNODE_API_INFO,LOTUS_PATH,LOTUS_MINER_PATH,LOTUS_WORKER_PATH,FIL_PROOFS_PARAMETER_CACHE ...

now you can assign deadline index you want to doPoSt

`RUST_LOG=info ./lotus-miner verify -di=?`
