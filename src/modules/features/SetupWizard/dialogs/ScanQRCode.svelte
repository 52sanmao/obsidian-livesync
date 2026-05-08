<script lang="ts">
    import DialogHeader from "@/lib/src/UI/components/DialogHeader.svelte";
    import Guidance from "@/lib/src/UI/components/Guidance.svelte";
    import Decision from "@/lib/src/UI/components/Decision.svelte";
    import Instruction from "@/lib/src/UI/components/Instruction.svelte";
    import UserDecisions from "@/lib/src/UI/components/UserDecisions.svelte";
        const TYPE_CLOSE = "close";
    type ResultType = typeof TYPE_CLOSE;
    type Props = {
        setResult: (result: ResultType) => void;
    };
    const { setResult }: Props = $props();
</script>

<DialogHeader title="Setup.ScanQRCode.Title" />
<Guidance message="Setup.ScanQRCode.Guidance" />
<Instruction message="Setup.ScanQRCode.Instruction" />
<UserDecisions>
    <Decision title="Setup.ScanQRCode.ButtonClose" important={true} commit={() => setResult(TYPE_CLOSE)} />
</UserDecisions>
