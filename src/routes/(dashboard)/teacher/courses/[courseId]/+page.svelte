<script lang="ts">
    import DescriptionForm from '$lib/components/DescriptionForm.svelte';

    import CategoryForm from '$lib/components/CategoryForm.svelte';
    import IconBadge from '$lib/components/IconBadge.svelte';
	import ImageForm from '$lib/components/ImageForm.svelte';
	import TitleForm from '$lib/components/TitleForm.svelte';
    import * as Alert from '$lib/components/ui/alert';
    import { AlertTriangle, CircleDollarSign, File, LayoutDashboard, ListChecks } from 'lucide-svelte'
	import PriceForm from '$lib/components/PriceForm.svelte';
	import AttachmentForm from '$lib/components/AttachmentForm.svelte';
	import ChapterForm from '$lib/components/ChapterForm.svelte';

    export let data;

    $: course = data.course
    $: requiredFields = [
        course.title,
        course.description,
        course.imageUrl,
        course.price,
        course.category,
        course.expand?.['chapters(course)']?.some((chapter) => chapter.isPublished)
    ]
    $: totalFields = requiredFields.length
    $: completedFields = requiredFields.filter(Boolean).length

    $: completedText = `(${completedFields}/${totalFields})`
    $: isComplete = requiredFields.every(Boolean)
</script>

{#if !course.isPublished}
    <Alert.Root class='bg-yellow-200/80 border border-yellow-300 rounded-none'>
        <AlertTriangle class='size-4' />
        <Alert.Title>Heads up!</Alert.Title>
        <Alert.Description>
            This course is un-published. It will not be visible to students.
        </Alert.Description>
    </Alert.Root>
{/if}

<div class="p-6">
    <div class="flex items-center justify-between">
        <div class="flex flex-col gap-y-2">
            <h1 class="text-2xl font-medium">Course setup</h1>
            <span class="text-sm text-muted-foreground">
                Complete all fields {completedText}
            </span>
        </div>
        <!-- Actions -->
    </div>

    <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mt-16">
        <div>
            <div class="flex items-center gap-x-2">
                <!-- IconBadge -->
                <IconBadge icon={LayoutDashboard} size='sm' />
                <h2 class="text-xl">Customize your course</h2>
            </div>
            <!-- TitleForm -->
            <TitleForm data={data.titleForm} />
            <!-- DescriptionForm -->
            <DescriptionForm data={data.descriptionForm} />
            <!-- ImageForm -->
            <ImageForm imageUrl={course.imageUrl} />
            <!-- CategoryForm -->
            <CategoryForm data={data.categoryForm} categories={data.categories} />
        </div>
        <div class="space-y-6">
            <div>
                <div class="flex items-center gap-x-2">
                    <IconBadge icon={ListChecks} size='sm' />
                    <h2 class="text-xl">Course Chapters</h2>
                </div>
                <!-- ChapterForm -->
                <ChapterForm
                    chapters={course.expand?.['chapters(course)'] ?? []}
                    data={data.chapterTitleForm}
                />
            </div>
            <div>
                <div class="flex items-center gap-x-2">
                    <IconBadge icon={CircleDollarSign} size='sm' />
                    <h2 class="text-xl">Sell your course</h2>
                </div>
                <!-- PriceForm -->
                <PriceForm data={data.priceForm} />

            </div>
            <div>
                <div class="flex items-center gap-x-2">
                    <IconBadge icon={File} size='sm' />
                    <h2 class="text-xl">Resources & Attachments</h2>
                </div>
                <!-- AttachmentForm -->
                 <AttachmentForm attachments={data.course?.expand?.['attachments(course)'] ?? []} />
            </div>
        </div>
    </div>
</div>

<!-- {data.course?.title}

{#each data.categories as category}
    <p>{category.name}</p>
{/each} -->